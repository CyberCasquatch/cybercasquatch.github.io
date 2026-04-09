---
layout: post
title:  "IATelligence — Ported to Claude"
date:   2026-04-09
author: C. Casquatch
comments: false
categories: Projects
tags: [claude, malware analysis]
---

# IATelligence — Ported to Claude

// Forking a malware analysis tool to use Anthropic's Claude API instead of OpenAI

Tool Python · Malware Analysis · AWS Bedrock

---

[fr0gger_](https://twitter.com/fr0gger_) built a neat proof-of-concept called IATelligence — a Python script that pulls the Import Address Table (IAT) from a PE file and sends each imported function to an LLM to explain its purpose and map it to MITRE ATT&CK techniques. The original used OpenAI's GPT-3. I forked it and ported it to Anthropic's Claude.

Fork: [github.com/CyberCasquatch/IATelligence_CC](https://github.com/CyberCasquatch/IATelligence_CC)

---

## What changed

The core logic is identical. The only meaningful changes are in how the API is called.

**Package swap**

```
# requirements.txt
# before
openai

# after
anthropic
```

**Client initialisation**

```python
# before
openai.api_key = ""

# after
client = anthropic.Anthropic(api_key="YOUR_API_KEY_HERE")
```

**API call structure**

```python
# before — OpenAI completion
result = openai.Completion.create(
    engine="text-davinci-003",
    prompt=prompt,
    ...
)
response_text = result["choices"][0]["text"].strip()

# after — Anthropic messages
message = client.messages.create(
    model="claude-sonnet-4-20250514",
    max_tokens=1024,
    messages=[{"role": "user", "content": prompt}]
)
response_text = message.content[0].text.strip()
```

The cost estimation line from the original was removed — Anthropic pricing varies by model so it didn't make sense to hardcode it.

---

## API access

The Claude API is pay-as-you-go and separate from any claude.ai subscription. You sign up at [platform.anthropic.com](https://platform.anthropic.com).

Current pricing per million tokens:

| Model | Input | Output |
|---|---|---|
| Haiku 4.5 | $1.00 | $5.00 |
| Sonnet 4.6 | $3.00 | $15.00 |
| Opus 4.6 | $5.00 | $25.00 |

Running the script against `calc.exe` (~33 functions) costs roughly **$0.05 USD**. `notepad.exe` (~259 functions) comes in around **$0.82 USD**. Cheap enough that you won't think about it.

---

## AWS Bedrock version

Australian accounts hit a GST/ABN wall when trying to add billing to platform.anthropic.com. Bedrock sidesteps this — same models, billed through AWS which handles Australian tax automatically.

A separate `iatelligencebedrock.py` is included in the fork for this.

**Additional requirement**

```
boto3
```

**Client swap**

```python
import boto3
import json

bedrock = boto3.client(
    service_name="bedrock-runtime",
    region_name="us-east-1",
    aws_access_key_id="YOUR_ACCESS_KEY_ID",
    aws_secret_access_key="YOUR_SECRET_ACCESS_KEY"
)
```

**Model ID**

Claude Sonnet 4.6 on Bedrock uses cross-region inference, so the plain model ID won't work. You need the region-prefixed inference profile:

```python
modelId="us.anthropic.claude-sonnet-4-6"
```

Keep `region_name="us-east-1"` in the client regardless of where your AWS account is based — cross-region inference routes through US East.

**Enabling the model**

In the AWS Console go to **Bedrock → Model access**. If you see an "Open in playground" button next to Claude Sonnet 4.6 rather than "Request access", access is already granted.

---

## Testing

Point it at something already on your machine:

```
python iatelligence.py C:\Windows\System32\calc.exe
```

```
[+] IAT Request from the file: C:\Windows\System32\calc.exe
[+] 33 functions will be requested to Claude!
[+] MD5: 5da8c98136d98dfec4716edd79c7145f
[+] SHA1: ed13af4a0a754b8daee4929134d2ff15ebe053cd
[+] SHA256: 58189cbd4e6dc0c7d8e66b6a6f75652fc9f4afc7ce0eba7d67d8c3feb0d5381f
[+] Imphash: 8eeaa9499666119d13b3f44ecd77a729
```

---

## Limitations

Same as the original — one API call per function, so large IATs are slow. Claude is a language model so MITRE mappings won't always be accurate. Treat output as a starting point for analysis.

---

Fork: [github.com/CyberCasquatch/IATelligence_CC](https://github.com/CyberCasquatch/IATelligence_CC)

## Original by [@fr0gger_](https://twitter.com/fr0gger_)
