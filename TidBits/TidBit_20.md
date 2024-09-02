# TidBit 20, created from weekly writing 20

Going through a digital forensics module in HTB.
First set of tasks was to RDP into a windows machine and access their Velociraptor portal with admin/password. 
I then created a new collection to gather artifacts labeled as "Windows.KapeFiles.Targets" using the _SANS_Triage configuration. 
 
Lastly, I examined the collected artifacts and found the scheduled task that was wanted by the question. Going back to read through where I could find scheduled tasks in a windows machine, helped. 
 
The second set of questions focuses on utilising Volatility for analysis. 
First used 
vol.py -f /home/htb-student/MemoryDumps/Win7-2515534d.vmem --profile=Win7SP1x64 pslist
to list all processes in memory dump. SEarching through this to find the parent ID of the @WanaDecryptor process. 
Then utilised vol.py -f /home/htb-student/MemoryDumps/Win7-2515534d.vmem --profile=Win7SP1x64 handles -p <PID> --object-type=File
and found the specific handle that the question was asking. 
lastly I used a generic command to search through a list of processes and their .dll files to find the parent process ID of a specific file. 
command: vol.py -f /home/htb-student/MemoryDumps/Win7-2515534d.vmem --profile=Win7SP1x64 dlllist
Instead of specifying -p <PID> | I wanted to list all of the processes and their dll files, because I did not know the ID of the parent process I was asked to look for. 
 
There would be a better way to do the last one, but I got lucky - the answer wasn't a long scroll up. 
The second room within the module focuses on some of Eric Zimmerman's tools. Mainly Timeline Explorer.
One of the first tasks is to use the Zone.Identifier information to find the changed name of the file 'uninstall.exe'. First we convert the file to a csv with 
.\MFTECmd.exe -f 'C:\Users\johndoe\Desktop\forensic_data\kape_output\D\$Extend\$J' --csv C:\Users\johndoe\Desktop\forensic_data\mft_analysis\ --csvf MFT-J.csv 
the above is an example - below is the empty command to fill with your own file paths. 
.\MFTECmd.exe -f 'FILE PATH TO .MFT OR OTHER' --csv <PATH TO WHERE YOU WANT TO PUT YOUR NEW CSV FILE> --csvf <NAMEOFFILE>.csv
Then we upload the csv into Timeline Explorer and filter on the Zone.Identifiers - from here we explore what is left and find the changed name of the file. 
 
This one tool me a little while to get - but it's actually really easy. I went to the HTB discord and searched for other people who might have had the same issue I was having. There were several people there that gave hints as to what you should be looking for. I am finding that this discord is really great for pushing your way through difficult questions. There's bound to be someone who has asked before you, so make sure you search through the backlog of chats to try and find some hints. 
I found the second a little easier to find - I think by exploring more of the software, it gets easier to filter different columns you know to have the answer. 
We transfer a file to a csv file and drop it into Timeline Explorer. Here, they want us to find a scheduled task with certain parameters. For this one, I just filtered on 'm' in the Executable Info column (this column suggested in the training section of the room)
 
For the last question we are using API monitor with an .apmx64 file. 
Tasked to find a specific process that 'discord.exe' had injected. 
I searched for the file in the training screenshot first, using the # column to get to the specific line that the screenshots start at. Then filed through the proceeding 'discord.exe' modules listed, to try and find the process that it had injected. 
 
Success. This room is completed.

[back](./TidBitMain.html)
