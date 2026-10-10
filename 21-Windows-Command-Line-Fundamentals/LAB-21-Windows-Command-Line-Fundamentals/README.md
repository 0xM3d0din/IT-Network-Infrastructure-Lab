# LAB 21 — Windows Command-Line Fundamentals

## Overview

This lab introduces the fundamentals of Windows Command Prompt (CMD) for IT Support and system administration.

The lab focuses on identifying a Windows workstation, inspecting system information, navigating directories, exploring command-line help, examining running processes, filtering command output, and saving diagnostic results to a text file.

The objective is to build a foundation for using command-line tools in everyday IT support scenarios.

## Objectives

By completing this lab, you will learn how to:

- Understand the role of Command Prompt in Windows administration.
- Retrieve basic computer and user information.
- Inspect Windows operating system details.
- Understand and display environment variables.
- Navigate directories and list files.
- Use built-in command-line help.
- List and filter running processes.
- Inspect a specific process using its Process ID (PID).
- Filter command output using `findstr`.
- Redirect command output to a text file.
- Verify saved diagnostic output.
- Follow a structured approach to basic system investigation.

## Lab Environment

**Environment Type:** Windows workstation  
**Operating System:** Windows 11 Pro  
**Primary Tool:** Command Prompt (CMD)  
**Additional Utilities:** `systeminfo`, `tasklist`, `findstr`, `more`, `type`

### Sample Workstation Information — Mock Data

The following values are fictional and are provided for demonstration purposes only.

| Property | Mock Value |
|---|---|
| Device Name | CLIENT-WS01 |
| Operating System | Windows 11 Pro |
| OS Build | 26100.x |
| System Architecture | x64-based PC |
| User Account | LABUSER |
| Domain Status | WORKGROUP |

Actual command output depends on the workstation and its current configuration.

## 1. Command Prompt Fundamentals

Command Prompt is a command-line interface that allows users to execute commands by entering text instead of interacting exclusively with graphical interfaces.

CMD is useful for:

- Retrieving system information.
- Navigating the filesystem.
- Investigating running processes.
- Filtering diagnostic output.
- Automating repetitive tasks through batch files.

### Open Command Prompt

1. Press `Win + R`.
2. Enter `cmd`.
3. Press Enter.

For the initial exercises, use a standard user session rather than Administrator privileges. Elevation should be used only when a task requires it.

## 2. Basic System Identification

### Identify the Computer

Command:

    hostname

Purpose:

Displays the hostname of the current computer.

Example output:

    CLIENT-WS01

### Identify the Current User

Command:

    whoami

Purpose:

Displays the identity of the current user in the form of a local computer or domain prefix followed by the username.

Example output:

    client-ws01\labuser

### Display the Windows Version

Command:

    ver

Purpose:

Displays the Windows version information available through CMD.

Example output:

    Microsoft Windows [Version 10.0.26100.x]

### Retrieve Detailed System Information

Command:

    systeminfo

Purpose:

Displays a detailed system summary, including operating system information, hardware details, memory information, boot information, network adapters, and other system properties.

Important fields include:

- Host Name
- OS Name
- OS Version
- System Manufacturer
- System Model
- Processor(s)
- Total Physical Memory
- Available Physical Memory
- Domain
- Network Card(s)

The output may include sensitive information, such as registered owner details, network configuration, and system identifiers. Avoid publishing the complete raw output in a public repository.

## 3. Environment Variables

Environment variables provide values that Windows and applications can reference during execution.

In CMD, an environment variable can be displayed using this syntax:

    %VARIABLE_NAME%

### Display the Computer Name

    echo %COMPUTERNAME%

Displays the computer name.

### Display the Username

    echo %USERNAME%

Displays the username associated with the current environment.

### Display the Operating System Environment Value

    echo %OS%

Typically returns:

    Windows_NT

This value identifies the Windows NT operating system family; it does not provide the Windows product version.

### Display the Process Architecture

    echo %PROCESSOR_ARCHITECTURE%

On a 64-bit Windows environment, this commonly returns:

    AMD64

This identifies the x64 architecture used by the current process environment. It does not mean that the processor must be manufactured by AMD.

### Compare `whoami` and `%USERNAME%`

The commands serve different purposes:

- `whoami` displays the current security identity, including its computer or domain context.
- `%USERNAME%` displays the username stored in the current environment.

## 4. Directory Navigation and File Listing

CMD can be used to navigate the filesystem without opening File Explorer.

### Display the Current Directory

    cd

Displays the current working directory.

### Display the User Profile Path

    echo %USERPROFILE%

Shows the path associated with the current user's profile.

### List Files and Directories

    dir

Displays files and directories in the current location.

The output can include:

- File and directory names
- File sizes
- Modification dates
- Number of files and directories
- Free space on the volume containing the directory

The reported free space is volume-level information, not the size of the current user directory.

### Move to the Parent Directory

    cd ..

Moves one level upward in the directory hierarchy.

### Return to a Named Directory

Example:

    cd LABUSER

The directory must exist under the current path for this relative command to work.

## 5. Command-Line Help

Learning how to access built-in documentation reduces reliance on memorizing commands.

### Display the General CMD Help List

    help

Displays a list of supported CMD commands and short descriptions.

Examples include:

| Command | Purpose |
|---|---|
| `CD` | Display or change the current directory |
| `DIR` | List files and directories |
| `COPY` | Copy files |
| `DRIVERQUERY` | Display driver information |
| `SYSTEMINFO` | Display system information |
| `TASKLIST` | Display running processes |
| `SC` | Query or configure services |
| `VER` | Display Windows version information |

The `HELP` list does not include every executable or diagnostic utility available in Windows.

### Request Help for a Specific Command

    hostname /?

Displays the usage information for `hostname`.

Not every command supports the same options. Some commands provide brief usage information, while others display detailed help.

### Display Help One Screen at a Time

    help | more

The pipe character (`|`) passes the output of the first command to the second command.

The `more` utility displays long output one screen at a time, allowing the user to review it without scrolling through the entire result at once.

## 6. Running Process Investigation

A process is an executing instance of a program or system component.

The Process ID (PID) is a numerical identifier assigned to a running process. A PID can change when a process is restarted.

### List Running Processes

    tasklist

Displays running processes, including their image names, PIDs, session information, and memory usage.

### Display the Process List Page by Page

    tasklist | more

Passes the process list to `more` for easier inspection.

### Search the Process List Using `findstr`

    tasklist | findstr /i "chrome"

Purpose:

Searches the text output of `tasklist` for lines containing the specified process name.

The `/i` option makes the search case-insensitive.

This method is useful for quickly locating matching process names in command output.

### Filter Processes by Image Name

    tasklist /FI "IMAGENAME eq chrome.exe"

Purpose:

Displays processes whose image name matches `chrome.exe`.

Here:

- `/FI` specifies a filter.
- `IMAGENAME` identifies the field being filtered.
- `eq` means equal to.
- `chrome.exe` is the requested image name.

Filtering through `tasklist` is more direct when the goal is to retrieve processes matching a specific field.

### Inspect a Specific Process

Example using a fictional PID:

    tasklist /FI "PID eq 1234" /V

Replace `1234` with the PID of the process being investigated.

The `/V` option requests verbose information, including additional details such as status, user name, CPU time, and window title when available.

Important considerations:

- A PID is temporary and can change between executions.
- `CPU Time` is accumulated processor time, not the current CPU utilization percentage.
- `Mem Usage` is a per-process memory measurement and is not sufficient by itself to diagnose a performance problem.
- An `Unknown` status or an unavailable window title does not automatically indicate a malfunction.

### Important Troubleshooting Principle

Multiple processes with the same image name do not necessarily indicate a problem. Modern applications, including web browsers, commonly use multiple processes for separate tasks and components.

Always correlate process information with the reported symptoms and other diagnostic evidence before deciding on an action.

Do not terminate processes solely because their memory usage is high.

## 7. Filtering System Information

Large command outputs can be difficult to review manually.

Filtering allows technicians to extract only the information needed for a specific support task.

### Retrieve Selected Operating System Details

Command:

    systeminfo | findstr /B /C:"OS Name" /C:"OS Version" /C:"System Type"

Purpose:

Extracts selected lines from the output of `systeminfo`.

Options:

- `/B` matches the search text at the beginning of a line.
- `/C:` specifies a literal search string.
- `|` sends the output of `systeminfo` to `findstr`.

Example output:

    OS Name:                       Microsoft Windows 11 Pro
    OS Version:                    10.0.26100 N/A Build 26100
    System Type:                   x64-based PC

The example illustrates the output format. Actual values depend on the target workstation.

## 8. Output Redirection

Output redirection allows command results to be saved to a file instead of being displayed only on the screen.

### Redirect Output Using `>`

Command:

    systeminfo | findstr /B /C:"OS Name" /C:"OS Version" /C:"System Type" > "%TEMP%\LAB21-System-QuickCheck.txt"

Purpose:

1. Runs `systeminfo`.
2. Filters the output to the selected lines.
3. Saves the filtered results to a text file inside the current user's temporary directory.

The `%TEMP%` environment variable identifies the temporary directory for the current user environment.

### Read the Saved File

Command:

    type "%TEMP%\LAB21-System-QuickCheck.txt"

Displays the contents of the text file.

### Understand the Redirection Operators

| Operator | Behavior |
|---|---|
| `>` | Creates or overwrites the destination file |
| `>>` | Appends output to the end of an existing file, creating it if necessary |
| `|` | Passes one command's output to another command |

Be careful when using `>` because it replaces existing file contents rather than appending to them.

## 9. Practical Support Scenario

### Scenario

A helpdesk technician needs to identify a Windows workstation, verify its operating system information, and check whether a specified application process is running.

The technician must collect the results and save a short operating system report for documentation.

### Investigation Workflow

1. Identify the computer with `hostname`.
2. Identify the current user with `whoami`.
3. Check the Windows version with `ver`.
4. Collect detailed information with `systeminfo`.
5. List processes with `tasklist`.
6. Locate a relevant application using `findstr` or a `tasklist` filter.
7. Retrieve selected operating system fields.
8. Save the filtered system information to a text file.
9. Read the file and verify that the information was captured correctly.

### Expected Outcome

The technician can identify the system, retrieve relevant command output, filter results, and produce a small text report suitable for an internal support record.

This exercise does not establish that a workstation is free of issues. Further investigation depends on the reported symptoms.

## 10. Verification Checklist

- [x] Opened Command Prompt without requiring elevation.
- [x] Retrieved the computer name using `hostname`.
- [x] Retrieved the current identity using `whoami`.
- [x] Displayed the Windows version using `ver`.
- [x] Collected detailed information using `systeminfo`.
- [x] Inspected environment variables.
- [x] Navigated directories using `cd`.
- [x] Listed directory contents using `dir`.
- [x] Reviewed command-line help using `help`.
- [x] Used `hostname /?` to inspect command usage.
- [x] Listed and filtered running processes.
- [x] Inspected a process using a PID filter and verbose output.
- [x] Filtered selected system information using `findstr`.
- [x] Redirected command output to a text file using `>`.
- [x] Verified saved output using `type`.

## Safety and Data Handling

- Do not execute commands with elevated privileges unless the task requires them.
- Do not terminate system processes without understanding their purpose.
- Do not assume that high memory usage alone indicates a fault.
- Avoid publishing raw `systeminfo` output because it can contain identifying and configuration details.
- Use fictional hostnames, usernames, addresses, and identifiers in public examples.
- Review the behavior of file operations before executing commands that overwrite or delete data.

## Key Takeaways

CMD provides a practical way to inspect Windows systems, retrieve diagnostic information, filter output, and preserve results for later review.

Commands such as `hostname`, `whoami`, `systeminfo`, `tasklist`, and `findstr` are useful building blocks for IT support workflows.

Output redirection makes it possible to save diagnostic results, while command-line help enables technicians to investigate unfamiliar commands independently.

The goal is not simply to memorize commands, but to understand what each command reveals and how its output supports a troubleshooting decision.

## Next Lab

**LAB 22 — Windows Network Troubleshooting**

The next lab will focus on Windows networking diagnostics using tools such as:

- `ipconfig`
- `ping`
- `nslookup`
- `tracert`

The objective will be to investigate IP configuration, connectivity, gateway reachability, and DNS resolution in structured troubleshooting scenarios.

---

**Project:** IT Network & Infrastructure Lab  
**Journey:** 100 Days of IT Infrastructure  
**Lab:** LAB 21 — Windows Command-Line Fundamentals  
**Focus:** CMD, System Information, Process Investigation, Filtering, and Output Redirection