# Process Dump
Windows reverse-engineering command-line tool to dump malware memory components back to disk for analysis. This is a common task for malware researchers who need to dump unpacked or injected code back to disk for analysis with static analysis tools such as IDA.

Process Dump works for 32 and 64 operating systems, uses an aggressive import reconstruction approach, and allows for dumping of regions without PE headers - in these cases PE headers and import tables will automatically be generated. Process Dump supports creation and use of a clean-hash database, so that dumping of clean files such as kernel32.dll can be skipped.

I'm maintaining an official compiled release on my website here:
  http://split-code.com/processdump.html

# Example Usage
Dump all modules from all processes (ignoring known clean modules):
   -   pd64.exe -system

 Dump all modules from a specific process identifier:
   -   pd64.exe -pid 0x18A

 Dump all modules by process name:
   -   pd64.exe -p .*chrome.*

 Build clean-hash database. These hashes will be used to exclude modules from dumping with the above commands:
   -   pd64.exe -db gen

 Dump code from a specific address in PID 0x1a3:
   -   pd64.exe -pid 0x1a3 -a 0xffb4000
        Generates two files (32 and 64 bit) that can be loaded for analysis in IDA with generated PE headers and
        generated import table:
                notepad_exe_x64_hidden_FFB40000.exe
                notepad_exe_x86_hidden_FFB40000.exe


# Help Page
Process Dump v1.5
  Copyright ® 2015, Geoff McDonald
  http://www.split-code.com/

Process Dump (pd.exe) is a tool used to dump both 32 and 64 bit executable modules
back to disk from memory within a process address space. This tool is able to find
and dump hidden modules, and it uses a clean hash database to exclude dumping of
known clean files. This tool uses an aggressive import reconstruction approach that
links all DWORD/QWORDs that point to an export in the process to the corresponding
export function.

Before first usage of this tool, when on the clean workstation the clean exclusing
hash database can be generated by either:
  pd -db gen
  pd -db genquick

Example Usage:
  pd -system
  pd -p chrome.exe
  pd -p "(?i).*chrome.*"
  pd -pid 419
  pd -pid 0x1a3
  pd -pid 0x1a3 -a 0x401000

Options:
  -pid (pid)  Dumps all modules not matching the clean hash database
      from the specified pid into the current working
      directory. Use a '0x' prefix to specify a hex PID.

  -p (regex)  Dumps all modules not matching the clean hash database
      from the process name found to match the filter into
      specified pid into the current working directory.

  -system   Dumps all modules not matching the clean hash database
      from all accessible processes into the working
      directory.

  -g    Forces generation of PE headers from scratch, ignoring existing headers.

  -v    Verbose.

  -nh   No header is printed in the output.

  -nr   Disable recursion on hash database directory add or
      remove commands.

  -ni   Disable import reconstruction.

  -db gen   Automatically processes a few common folders as well as
      all the currently running processes and adds the found
      module hashes to the clean hash database. It will add
      all files recursively in: 
        %WINDIR% 
        %HOMEPATH% 
        C:\Program Files\ 
        C:\Program Files (x86)\ 
      As well as all modules in all running processes 

  -db genquick  Adds the hashes from all modules in all processes to
      the clean hash database. Run this on a clean system.

  -db add (dir) Adds all the files in the specified directory
      recursively to the clean hash database. 

  -db rem (dir) Removes all the files in the specified directory
      recursively from the clean hash database. 

  -db clean Clears the clean hash database.

  -db ignore  Ignores the clean hash database when dumping a process
      this time.  All modules will be dumped even if a match
      is found.

