![Blog Banner Image](/banner-without-background.png)

# `DevOps Strategies for IBM MQ Patching in the BFSI Industry`

My journey as a **DevOps engineer** in the *Banking, Financial Services and Insurance (BFSI)* sector over the past three years has been both exciting and challenging. Beyond addressing the typical demands of our clients, I've encountered new obstacles that have enhanced my experience. 

A key responsibility I've undertaken is **IBM MQ patching**, an important process for ensuring the security and efficiency of our messaging systems. In this blog, I aim to share my insights on IBM MQ patching, the lessons I've learned, and offer tips that could benefit others facing similar challenges.

---

### `What is IBM MQ?`
- IBM MQ, previously known as WebSphere MQ, is a strong messaging middleware that helps different applications, systems and services communicate with each other across various environments. It ensures messages are exchanged reliably and securely, no matter the platform, programming language or network conditions. 

--- 
### `What Does Patching IBM MQ Mean?`

Patching IBM MQ means updating or fixing an existing IBM MQ installation. These updates called patches, are released by IBM to solve specific problems, improve performance, fix security issues or add minor improvements to the software.

- #### Types of Patches
    - **Fix Packs:** Regularly released updates that include cumulative fixes for known issues and may also add minor enhancements.
    ---
    - **Interim Fixes (IFix):** Targeted updates that address critical issues or vulnerabilities in a more immediate and specific manner.
    ---
    - **Security Updates:** Critical updates specifically aimed at addressing security vulnerabilities.

--- 
### `Why Do We Need to Apply a Patch?`
Applying patches to IBM MQ is crucial to ensure the system remains secure, stable, and optimized. Here's why patching is important

- **Security:** Patches often address vulnerabilities that can be exploited by malicious actors. By applying security updates, organizations protect their IBM MQ systems from potential breaches or attacks.
---
- **Bug Fixes:** Fix Packs and Interim Fixes resolve known issues or bugs in the software, preventing disruptions in critical messaging workflows and improving system reliability.
---
- **Performance Improvements:** Some patches include optimizations that enhance the overall performance of IBM MQ, ensuring smooth operations even under high loads.
---
- **Compliance Requirements:** Many industries mandate that organizations keep their software updated to comply with security and operational standards. Regular patching ensures adherence to these regulations.
---
- **Feature Enhancements:** Fix Packs sometimes introduce new features or minor enhancements that improve the usability and functionality of IBM MQ. Keeping the software updated allows organizations to benefit from these innovations.
---
Neglecting to patch IBM MQ can leave the system exposed to vulnerabilities, reduce performance, and hinder the reliability of critical business operations. Therefore, applying patches is not just a maintenance task but an essential practice for the secure and efficient functioning of IBM MQ.

--- 
### `Patching Process Overview`

This document describes the detailed steps for applying patches and performing rollbacks on a server. It covers preconditions, error handling and automation opportunities to ensure the process is clear and efficient.

- **Pre-requisite Checks**
    ---
    - **Artifact Directory Validation:** Verify if the artifact download directory exists.   
    **Action:** If missing, create the directory.
    ---
    - **Backup Directory Validation:** Check for the existence of backup directory.   
    Action: If the directory is not present, create it.
    ---
    - **User Validation:** Confirm that process is being executed by the target user.   
    **Action:** If a different user is detected, log an error and terminate the process.
    ---
    - **Server Type Identification:** Determine whether server is active or passive.   
    **Automation Opportunity:** Use a script to identify server type automatically.

---

### `Patch Application Process`
Precondition: All pre-requisite checks must be completed successfully before proceeding.

- **Service State Snapshot:** Capture the current state of services, storing snapshot in a variable and saving it as a file (ServicesState).
---
- **Backup Creation:** Backup only the services that are currently running.
Automation Opportunity: Skip inactive services using a script to reduce manual effort.
---
- **Service Shutdown:** Stop all running services and verify they are completely stopped.
Error Handling: Log an error and retry stopping services if any remain active.
---
- **Binary File Preparation:** Transfer, pull or download the required binary file from the specified GitHub repository or download URL to the server.
Action: Verify the file transfer and log success or failure. Extract the file into the target directory.
Action: Log an error if extraction fails.
---
- **Patch Execution:** Navigate to target directory and execute patching command.
Error Handling: If the command fails, log the issue and prepare for rollback.
---
- **Patch Verification:** Confirm that patch has been applied successfully.
---
- **Service Restoration:** Restart only the services that were running prior to the patching process.
Postcondition: Verify that all services are restored to their original state.

---
### `Rollback Process`

- **Trigger:** Rollback is performed only if the patch application fails or critical errors occur.
---
- **Service State File Verification:** Check if ServicesState file is present in the target directory.   
**Action:** If missing, terminate the rollback process with an error, as the file is crucial for restoration.
---
- **Service Shutdown:** Stop all running services.   
**Error Handling:** Verify and log that services are fully stopped.
---
- **Patch File Identification:** List all files that were part of the applied patch.   
**Action:** Display these files to inform the user of what will be deleted.
---
- **Rollback Execution:** Execute the rollback command.   
**Verification:** Confirm rollback by checking MQ version or other relevant criteria.   
**Postcondition:** Ensure the system is restored to its state prior to the patch application.

---
### `Flow of Patching Process`

![Flow Diagram](/flow-without-background.png)

---

### `Configured Solution`
The solution I developed uses **SVN**, **IBM MQ**, and **IBM DevOps Deploy** tools, with **shell scripting** as the main automation language.

In this solution, SVN efficiently stores and retrieves target binary files. I started by setting up a component process in IBM DevOps Deploy, using the shell script I created. Once the component process was in place, I set up an application process in IBM DevOps Deploy, which calls the component process on the target agent—a virtual machine (VM) where IBM MQ is installed.

> **Note:** *For security reasons, I can't share detailed information about the pipeline or its logs. However, I have included the shell scripts I developed to implement the solution, along with dummy logs for reference.*

![Steps](/solution.png)


### 🔔 `Important Commands`   
𝐇𝐞𝐫𝐞 𝐚𝐫𝐞 𝐬𝐨𝐦𝐞 𝐞𝐬𝐬𝐞𝐧𝐭𝐢𝐚𝐥 𝐜𝐨𝐦𝐦𝐚𝐧𝐝𝐬 𝐟𝐨𝐫 𝐦𝐚𝐧𝐚𝐠𝐢𝐧𝐠 𝐈𝐁𝐌 𝐌𝐐 𝐬𝐞𝐫𝐯𝐢𝐜𝐞𝐬:     

1. **`dspmq`** : *𝚃𝚑𝚒𝚜 𝚌𝚘𝚖𝚖𝚊𝚗𝚍 𝚕𝚒𝚜𝚝 𝚊𝚕𝚕 𝚝𝚑𝚎 𝚜𝚎𝚛𝚟𝚒𝚌𝚎𝚜 𝚠𝚑𝚒𝚌𝚑 𝚊𝚛𝚎 𝚌𝚘𝚗𝚏𝚒𝚐𝚞𝚛𝚎𝚍 𝚘𝚗 𝚜𝚢𝚜𝚝𝚎𝚖 𝚠𝚒𝚝𝚑 𝚝𝚑𝚎𝚒𝚛 𝚗𝚊𝚖𝚎 𝚊𝚗𝚍 𝚌𝚞𝚛𝚛𝚎𝚗𝚝 𝚜𝚝𝚊𝚝𝚞𝚜.*   
---
2. **`dmpmqcfg -m <Service Name> <Target Path>/<FileName>.txt`**: *𝚃𝚑𝚒𝚜 𝚌𝚘𝚖𝚖𝚊𝚗𝚍 𝚐𝚎𝚗𝚎𝚛𝚊𝚝𝚎 𝚋𝚊𝚌𝚔𝚞𝚙 𝚘𝚏 𝚜𝚎𝚛𝚟𝚒𝚌𝚎 𝚒𝚗 .𝚝𝚡𝚝 𝚏𝚒𝚕𝚎.*   
---
3. **`dmpmqcfg -m <Service Name> <Target Path>/<FileName>.mqsc`** : *𝚃𝚑𝚒𝚜 𝚌𝚘𝚖𝚖𝚊𝚗𝚍 𝚐𝚎𝚗𝚎𝚛𝚊𝚝𝚎 𝚋𝚊𝚌𝚔𝚞𝚙 𝚘𝚏 𝚜𝚎𝚛𝚟𝚒𝚌𝚎 𝚒𝚗 .𝚖𝚚𝚜𝚌 𝚏𝚒𝚕𝚎.*   
---
4. **`endmqm -i <Service Name>`**: *𝚃𝚑𝚒𝚜 𝚌𝚘𝚖𝚖𝚊𝚗𝚍 𝚜𝚝𝚘𝚙𝚜 𝚂𝚎𝚛𝚟𝚒𝚌𝚎*   
---
5. **`rpm --prefix=<MQ_INSTALLATION_PATH> -ivh <Files Names>`**: *𝚃𝚑𝚒𝚜 𝚌𝚘𝚖𝚖𝚊𝚗𝚍 𝚊𝚙𝚙𝚕𝚢 𝚙𝚊𝚝𝚌𝚑 𝚘𝚗 𝙼𝚀*   
---
6. **`strmqm <Service Name>`**: *𝚃𝚑𝚒𝚜 𝚌𝚘𝚖𝚖𝚊𝚗𝚍 𝚜𝚝𝚊𝚛𝚝 𝚜𝚎𝚛𝚟𝚒𝚌𝚎*   
---
7. **`rpm -qa | grep <ROLLBACK_VERSION>`**: *𝚃𝚑𝚒𝚜 𝚌𝚘𝚖𝚖𝚊𝚗𝚍 𝚠𝚒𝚕𝚕 𝚕𝚒𝚜𝚝 𝚊𝚕𝚕 𝚝𝚑𝚎 𝚏𝚒𝚕𝚎𝚜 𝚝𝚑𝚊𝚝 𝚊𝚛𝚎 𝚐𝚘𝚒𝚗𝚐 𝚝𝚘 𝚋𝚎 𝚛𝚘𝚕𝚕𝚋𝚊𝚌𝚔𝚎𝚍*   
---
8. **`rpm -qa | grep <ROLLBACK_VERSION> | xargs rpm -ev`**: *𝚃𝚑𝚒𝚜 𝚌𝚘𝚖𝚖𝚊𝚗𝚍 𝚠𝚒𝚕𝚕 𝚙𝚛𝚒𝚗𝚝 𝚕𝚒𝚜𝚝 𝚊𝚗𝚍 𝚛𝚘𝚕𝚕𝚋𝚊𝚌𝚔 𝚝𝚑𝚎 𝚏𝚒𝚕𝚎𝚜 𝚝𝚑𝚊𝚝 𝚊𝚛𝚎 𝚋𝚎𝚒𝚗𝚐 𝚊𝚙𝚙𝚕𝚒𝚎𝚍 𝚒𝚗 𝚁𝙾𝙻𝙻𝙱𝙰𝙲𝙺_𝚅𝙴𝚁𝚂𝙸𝙾𝙽*
---

### 𝐒𝐡𝐞𝐥𝐥 𝐒𝐜𝐫𝐢𝐩𝐭 𝐭𝐨 𝐚𝐩𝐩𝐥𝐲 𝐩𝐚𝐭𝐜𝐡
```bash
#!/usr/bin/env bash

# -----------------------------------------------------------------------------
# Author: Aniket Kakde
# Description: This script performs patching operations on IBM MQ, including
#              pre-requisite checks, backup, patch application, and service management.
# License: Copyright (C) 2025 Aniket Kakde. All rights reserved.
# -----------------------------------------------------------------------------

# declare all variables here
DOWNLOAD_ARTIFACTS_DIRECTORY="home/mqm/mq_patching/downloadartifacts" # this will be used to transfer updated binary file
BACKUP_DIRECTORY="home/mqm/mq_patching/backup" # all the backups will be taken here
TARGET_USER="mqm" # mqm user is going to be used for patching
FIX_PATCHED_VERSION=("9.2.0.30" "9.3.0.26") # these are the latest versions of IBM MQ if current system's version matches any of these then there is no need to apply patches
MQ_INSTALLATION_PATH="/opt/mqm"

echo "==============================================================================="
echo """
                      .-----------------.
                      | IBM MQ Patching |
                      '-----------------'
"""
# pre-requisites checks
echo "…………………………………………………………………………………………………………………………………………………………………………………………………………"
echo "➤  Check if ${DOWNLOAD_ARTIFACTS_DIRECTORY} is present or not"
if [[ -d "${DOWNLOAD_ARTIFACTS_DIRECTORY}" ]]; then
    echo "  ⤷ 📂 Download Artifact Directory :- ${DOWNLOAD_ARTIFACTS_DIRECTORY}"
else
    mkdir -p "${DOWNLOAD_ARTIFACTS_DIRECTORY}"
    if [[ -d "${DOWNLOAD_ARTIFACTS_DIRECTORY}" ]]; then
        echo "  ⤷ 📂 Download Artifact Directory :- ${DOWNLOAD_ARTIFACTS_DIRECTORY}"
    else
        echo "  ⤷ ❌ Error:- ${DOWNLOAD_ARTIFACTS_DIRECTORY} does not exists"
        exit 1
    fi
fi

echo "…………………………………………………………………………………………………………………………………………………………………………………………………………"
echo "➤  Check if ${BACKUP_DIRECTORY} is present or not"
if [[ -d "${BACKUP_DIRECTORY}" ]]; then
    echo "  ⤷ 🗂️  Backup Directory :- ${BACKUP_DIRECTORY}"
else
    mkdir -p "${BACKUP_DIRECTORY}"
    if [[ -d "${BACKUP_DIRECTORY}" ]]; then
        echo "  ⤷ 🗂️  Backup Directory :- ${BACKUP_DIRECTORY}"
    else
        echo "  ⤷ ❌ Error:- ${BACKUP_DIRECTORY} does not exists"
        exit 1
    fi
fi

echo "…………………………………………………………………………………………………………………………………………………………………………………………………………"
echo "➤  Check if Current User is equal to ${TARGET_USER} or not"
current_user="mqm"
if [[ "${current_user}" == "${TARGET_USER}" ]]; then
    echo "  ⤷ 👤 Target User :- ${TARGET_USER}"
else
    echo "  ⤷ ❌ Error :- ${TARGET_USER} ≠ ${current_user}"
    exit 1
fi

echo "…………………………………………………………………………………………………………………………………………………………………………………………………………"
echo "➤  Check if Current Version of IBM MQ is in ${FIX_PATCHED_VERSION[@]} or not"
#current_version=$(dspmqver | grep "Version:" | awk '{print $2}')
current_version="9.2.0.29"
for version in "${FIX_PATCHED_VERSION[@]}"; do
    if [[ "$version" == "$current_version" ]]; then
        found=true
        break
    fi
done

if [[ "$found" == "true" ]]; then
    echo "  ⤷ 🔍 Found ${current_version} is in ${FIX_PATCHED_VERSION[@]}, there is no need to apply patch"
    exit 0
else
    echo "  ⤷ 🔔 ${current_version} is not in ${FIX_PATCHED_VERSION[@]}, proceed to apply patch"
    exit 1
fi

echo "…………………………………………………………………………………………………………………………………………………………………………………………………………"
echo "Service Details Below 👇 "
# Create a snapshot of current services, snapshot will be stored in file as well in variable
current_service_status=""
Services=$(dspmq)

while IFS= read -r line; do
    service_name=$(echo "$line" | awk '{print $1}' | sed 's/QMNAME//g' | sed 's/[()]//g')
    service_status=$(echo "$line" | awk '{$1=""; print $0}' | sed 's/STATUS//g' | sed 's/[()]//g')
    echo "…………………………………………………………………………………………………………………"
    echo "|-- Service Name → $service_name"
    echo "    ⤷ Service Status → $service_status"
    current_service_status="${current_service_status}#$service_name;$service_status"
done <<< $Services

echo "…………………………………………………………………………………………………………………"

current_service_status="${current_service_status#\#}"

echo "📷 Current Services Snapshot :- ${current_service_status}"
echo "$current_service_status" > "ServiceState.txt"

if [[ -f "ServiceState.txt" ]]; then
    echo "📋 ServiceState.txt created"
else
    echo "❌ Error Failed to create ServiceState.txt"
    exit 1
fi
echo "…………………………………………………………………………………………………………………………………………………………………………………………………………"
# take backup of those qmanager/services which are in running state
echo "Taking Backup of Services which are in running state"
timestamp=$(date '+%Y_%m_%d_%H_%M_%S')

while IFS= read -r line; do
    echo "…………………………………………………………………………………………………………………"
    service_name=$(echo "$line" | awk '{print $1}' | sed 's/QMNAME//g' | sed 's/[()]//g' | awk '{$1=$1; print}')
    service_status=$(echo "$line" | awk '{$1=""; print $0}' | sed 's/STATUS//g' | sed 's/[()]//g' | awk '{$1=$1; print}')

    if [[ "$service_status" == "Running" ]]; then
        echo "🔊 Service :- $service_name is in $service_status, proceed to take backup"

        complete_backup_path="${BACKUP_DIRECTORY}/QM_Backup_${timestamp}/${service_name}"

        if [[ ! -d "${complete_backup_path}" ]]; then
            mkdir -p "$complete_backup_path"

            if [[ -d "${complete_backup_path}" ]]; then
                echo "  ⮡ ${complete_backup_path} created successfully"
            else
                echo "  ⮡ Error unable to create ${complete_backup_path}"
                exit 1
            fi
        fi

        dmpmqcfg -m "${service_name}" > "${complete_backup_path}/${service_name}.txt"
        dmpmqcfg -m "${service_name}" > "${complete_backup_path}/${service_name}.mqsc"

        if [[ -f "${complete_backup_path}/${service_name}.txt" ]]; then
            echo "     ⮡ ${complete_backup_path}/${service_name}.txt -> Backup Taken"
        else
            echo "     ⮡ ${complete_backup_path}/${service_name}.txt -> Backup Failed"
            exit 1
        fi

        if [[ -f "${complete_backup_path}/${service_name}.mqsc" ]]; then
            echo "     ⮡ ${complete_backup_path}/${service_name}.mqsc -> Backup Taken"
        else
            echo "     ⮡ ${complete_backup_path}/${service_name}.mqsc -> Backup Failed"
            exit 1
        fi
    else
        echo "🔔 Service :- $service_name is in $service_status, backup not will be taken"
    fi

done <<< $Services

echo "…………………………………………………………………………………………………………………………………………………………………………………………………………"
# stop services.
# we will only those services which are in state running.
echo "Stop Services"
while IFS= read -r line; do
    service_name=$(echo "$line" | awk '{print $1}' | sed 's/QMNAME//g' | sed 's/[()]//g' | awk '{$1=$1; print}')
    service_status=$(echo "$line" | awk '{$1=""; print $0}' | sed 's/STATUS//g' | sed 's/[()]//g' | awk '{$1=$1; print}')

    echo "…………………………………………………………………………………………………………………"
    if [[ "${service_status}" == "Running" ]]; then
        echo "🔔 Service Name :- ${service_name} is in ${service_status}, proceeding to stop service"
        endmqm -i "${service_name}"
        flag=flase
        for((i=0;i<10;i++)){
            temp_service_status=$(dspmq | grep "${service_name}" | awk '{print $2}' | awk '{$1=$1; print}' | sed 's/STATUS//g' | sed 's/[()]//g')
            if [[ "$temp_service_status" != "Running" ]]; then
                flag=true
                break
            fi
            echo "      ⏳ waiting for service to stop"
            sleep 5
        }

        # making sure service is stopped
        get_current_status=$(dspmq | grep "${service_name}" | awk '{print $2}' | awk '{$1=$1; print}' | sed 's/STATUS//g' | sed 's/[()]//g')

        if [[ "$get_current_status" != "Running" ]]; then
            echo "  ✅ Service :- ${service_name} Status :- ${get_current_status}"
        else
            echo "  ❌ Error Service :- ${service_name} Status :- ${get_current_status}, unable to stop service"
            exit 1
        fi
    else
        echo " 🚨 Service Name :- ${service_name} is in ${service_status}, No need to stop service"
    fi

done <<< $Services

echo "…………………………………………………………………………………………………………………………………………………………………………………………………………"
echo "Prepare Binary File"
# check if there single.tar.gz file is present or not
echo "…………………………………………………………………………………………………………………"
file_count=$(ls | grep "tar.gz" | wc -l)

if [[ "$file_count" != "1" ]]; then
    echo "Error :- There are more or less than 1 tar.gz file Current File Count :- ${file_count}"
    exit 1
fi

tar_file_name=$(ls | grep "tar.gz")

echo "🗂️  Tar File Name :- ${tar_file_name}"


extract_target_directory=""

for version in "${FIX_PATCHED_VERSION[@]}"; do
    trimmed_version=$(echo "$version" | cut -d '.' -f1-3)

    echo "${tar_file_name}" | grep -q "$trimmed_version"

    if [[ "$?" == "0" ]]; then
        extract_target_directory="MQ${version}"
        extract_target_directory=${extract_target_directory//./}
    fi
done

echo "…………………………………………………………………………………………………………………"

echo "Tar File will be extracted into :- ${extract_target_directory}"
mkdir -p "${extract_target_directory}"

if [[ ! -d "${extract_target_directory}" ]]; then
    echo "❌ Error Directory Missing :- ${extract_target_directory}"
    exit 1
fi

tar -xvf ${tar_file_name} -C "${extract_target_directory}"
if [[ "$?" == "0" ]]; then
    echo "✅ Tar file extracted successfully"
else
    echo "❌ Error while extracting tar file"
fi

echo "…………………………………………………………………………………………………………………………………………………………………………………………………………"
echo "🛠️  Apply Patch"
echo "…………………………………………………………………………………………………………………"
echo "Version Before Applying Patch :- $current_version"
echo "…………………………………………………………………………………………………………………"
cd "${extract_target_directory}"
echo "Executing Command :- rpm --prefix=${MQ_INSTALLATION_PATH} -ivh MQSeriesRuntime-* MQSeriesJava* MQSeriesSDK* MQSeriesServer* MQSeriesJRE* MQSeriesSamples* MQSeriesGSKit*"
rpm --prefix=${MQ_INSTALLATION_PATH} -ivh MQSeriesRuntime-* MQSeriesJava* MQSeriesSDK* MQSeriesServer* MQSeriesJRE* MQSeriesSamples* MQSeriesGSKit*
if [[ "$?" == "0" ]]; then
    echo "✅ Command Executed Successfully:- rpm --prefix=/opt/mqm/ -ivh MQSeriesRuntime-* MQSeriesJava* MQSeriesSDK* MQSeriesServer* MQSeriesJRE* MQSeriesSamples* MQSeriesGSKit*"
else
    echo "❌ Error while executing command:- rpm --prefix=/opt/mqm/ -ivh MQSeriesRuntime-* MQSeriesJava* MQSeriesSDK* MQSeriesServer* MQSeriesJRE* MQSeriesSamples* MQSeriesGSKit*"
    exit 1
fi
echo "…………………………………………………………………………………………………………………"
version_after_patching=$(dspmqver | grep "Version:" | awk '{print $2}')
echo "Version After Patching :- $version_after_patching"
echo "…………………………………………………………………………………………………………………………………………………………………………………………………………"
# start services which were running before applying patch
echo "Start Services"
while IFS= read -r line; do
    service_name=$(echo "$line" | awk '{print $1}' | sed 's/QMNAME//g' | sed 's/[()]//g' | awk '{$1=$1; print}')
    service_status=$(echo "$line" | awk '{$1=""; print $0}' | sed 's/STATUS//g' | sed 's/[()]//g' | awk '{$1=$1; print}')

    echo "…………………………………………………………………………………………………………………"
    if [[ "${service_status}" == "Running" ]]; then
        echo "🔔 Service Name :- ${service_name} was in ${service_status} before patching, proceeding to start service"
        flag=flase

        strmqm "${service_name}"

        for((i=0;i<10;i++)){
            temp_service_status=$(dspmq | grep "${service_name}" | awk '{print $2}' | awk '{$1=$1; print}' | sed 's/STATUS//g' | sed 's/[()]//g')
            if [[ "$temp_service_status" == "Running" ]]; then
                flag=true
                break
            fi
            echo "      ⏳ waiting for service to start" 
            sleep 5
        }

        # making sure service is ruuning
        get_current_status=$(dspmq | grep "${service_name}" | awk '{print $2}' | awk '{$1=$1; print}' | sed 's/STATUS//g' | sed 's/[()]//g')

        if [[ "$get_current_status" == "Running" ]]; then
            echo "  ✅ Service :- ${service_name} Status :- ${get_current_status}"
        else
            echo "  ❌ Error Service :- ${service_name} Status :- ${get_current_status}, unable to stop service"
            exit 1
        fi
    else
        echo " 🚨 Service Name :- ${service_name} was in ${service_status} before patching, No need to start service"
    fi
done <<< $Services

echo "…………………………………………………………………………………………………………………………………………………………………………………………………………"
echo "End of Patching"
echo "==============================================================================="
```
---
### 𝐒𝐚𝐦𝐩𝐥𝐞 𝐨𝐮𝐭𝐩𝐮𝐭
```text
===============================================================================

                      .-----------------.
                      | IBM MQ Patching |
                      '-----------------'

………………………………………………………………………………………………………………………………………………………………………………………………………… 
➤  Check if home/mqm/mq_patching/downloadartifacts is present or not
  ⤷ 📂 Download Artifact Directory :- /home/mqm/mq_patching/downloadartifacts
………………………………………………………………………………………………………………………………………………………………………………………………………… 
➤  Check if home/mqm/mq_patching/backup is present or not
  ⤷ 🗂️  Backup Directory :- /home/mqm/mq_patching/backup
………………………………………………………………………………………………………………………………………………………………………………………………………… 
➤  Check if Current User is equal to mqm or not
  ⤷ 👤 Target User :- mqm
………………………………………………………………………………………………………………………………………………………………………………………………………… 
➤  Check if Current Version of IBM MQ is in 9.2.0.30 9.3.0.26 or not
  ⤷ 🔔 9.2.0.29 is not in 9.2.0.30 9.3.0.26, proceed to apply patch
…………………………………………………………………………………………………………………………………………………………………………………………………………
Service Details Below 👇
…………………………………………………………………………………………………………………
|-- Service Name → TEST1
        ⤷ Service Status →  Ended immediately
…………………………………………………………………………………………………………………
|-- Service Name → TEST2
        ⤷ Service Status →  Ended immediately
…………………………………………………………………………………………………………………
|-- Service Name → TEST3
        ⤷ Service Status →  Ended immediately
…………………………………………………………………………………………………………………
|-- Service Name → TEST4
        ⤷ Service Status →  Running
…………………………………………………………………………………………………………………
|-- Service Name → TEST5
        ⤷ Service Status →  Running
…………………………………………………………………………………………………………………
📷 Current Services Snapshot :- TEST1; Ended immediately#TEST2; Ended immediately#TEST3; Ended immediately#TEST4; Running#TEST5; Running
📋 ServiceState.txt created
…………………………………………………………………………………………………………………………………………………………………………………………………………
Taking Backup of Services which are in running state
…………………………………………………………………………………………………………………
🔔 Service :- TEST1 is in Ended immediately, backup not will be taken
…………………………………………………………………………………………………………………
🔔 Service :- TEST2 is in Ended immediately, backup not will be taken
…………………………………………………………………………………………………………………
🔔 Service :- TEST3 is in Ended immediately, backup not will be taken
…………………………………………………………………………………………………………………
🔊 Service :- TEST4 is in Running, proceed to take backup
  ⮡ home/mqm/mq_patching/backup/QM_Backup_2025_01_17_18_32_34/TEST4 created successfully
     ⮡ home/mqm/mq_patching/backup/QM_Backup_2025_01_17_18_32_34/TEST4/TEST4.txt -> Backup Taken
     ⮡ home/mqm/mq_patching/backup/QM_Backup_2025_01_17_18_32_34/TEST4/TEST4.mqsc -> Backup Taken
…………………………………………………………………………………………………………………
🔊 Service :- TEST5 is in Running, proceed to take backup
  ⮡ home/mqm/mq_patching/backup/QM_Backup_2025_01_17_18_32_34/TEST5 created successfully
     ⮡ home/mqm/mq_patching/backup/QM_Backup_2025_01_17_18_32_34/TEST5/TEST5.txt -> Backup Taken
     ⮡ home/mqm/mq_patching/backup/QM_Backup_2025_01_17_18_32_34/TEST5/TEST5.mqsc -> Backup Taken
…………………………………………………………………………………………………………………………………………………………………………………………………………
Stop Services
…………………………………………………………………………………………………………………
 🚨 Service Name :- TEST1 is in Ended immediately, No need to stop service
…………………………………………………………………………………………………………………
 🚨 Service Name :- TEST2 is in Ended immediately, No need to stop service
…………………………………………………………………………………………………………………
 🚨 Service Name :- TEST3 is in Ended immediately, No need to stop service
…………………………………………………………………………………………………………………
🔔 Service Name :- TEST4 is in Running, proceeding to stop service
      ⏳ waiting for service to stop
      ⏳ waiting for service to stop
      ⏳ waiting for service to stop
      ⏳ waiting for service to stop
      ⏳ waiting for service to stop
      ⏳ waiting for service to stop
      ⏳ waiting for service to stop
      ⏳ waiting for service to stop
      ⏳ waiting for service to stop
      ⏳ waiting for service to stop
  ✅ Service :- TEST4 Status :- Ended immediately
…………………………………………………………………………………………………………………
🔔 Service Name :- TEST5 is in Running, proceeding to stop service
      ⏳ waiting for service to stop
      ⏳ waiting for service to stop
      ⏳ waiting for service to stop
      ⏳ waiting for service to stop
      ⏳ waiting for service to stop
      ⏳ waiting for service to stop
      ⏳ waiting for service to stop
      ⏳ waiting for service to stop
      ⏳ waiting for service to stop
      ⏳ waiting for service to stop
  ✅ Service :- TEST5 Status :- Ended immediately
…………………………………………………………………………………………………………………………………………………………………………………………………………
Prepare Binary File
…………………………………………………………………………………………………………………
🗂️  Tar File Name :- 9.3.0-IBM-MQ-LinuxX64-FP0026.tar.gz
…………………………………………………………………………………………………………………
Tar File will be extracted into :- MQ93026
Advanced/RDQM/
Advanced/RDQM/MQSeriesRDQM-U93026-9.3.0-26.x86_64.rpm\r
Advanced/RDQM/PreReqs/
Advanced/RDQM/PreReqs/el7/
Advanced/RDQM/PreReqs/el7/drbd-utils-9/
Advanced/RDQM/PreReqs/el7/drbd-utils-9/drbd-bash-completion-9.27.0-1.el7.x86_64.rpm\r
Advanced/RDQM/PreReqs/el7/drbd-utils-9/drbd-pacemaker-9.27.0-1.el7.x86_64.rpm\r
Advanced/RDQM/PreReqs/el7/drbd-utils-9/drbd-udev-9.27.0-1.el7.x86_64.rpm\r
Advanced/RDQM/PreReqs/el7/drbd-utils-9/drbd-utils-9.27.0-1.el7.x86_64.rpm\r
Advanced/RDQM/PreReqs/el7/drbd-utils-9/\r
Advanced/RDQM/PreReqs/el7/kmod-drbd-9/
Advanced/RDQM/PreReqs/el7/kmod-drbd-9/kmod-drbd-9.1.22_3.10.0_1062-1.x86_64.rpm\r
Advanced/RDQM/PreReqs/el7/kmod-drbd-9/kmod-drbd-9.1.22_3.10.0_1127-1.x86_64.rpm\r
Advanced/RDQM/PreReqs/el7/kmod-drbd-9/kmod-drbd-9.1.22_3.10.0_1160-1.x86_64.rpm\r
Advanced/RDQM/PreReqs/el7/kmod-drbd-9/kmod-drbd-9.1.22_3.10.0_957-1.x86_64.rpm\r
Advanced/RDQM/PreReqs/el7/kmod-drbd-9/modver\r
Advanced/RDQM/PreReqs/el7/kmod-drbd-9/\r
Advanced/RDQM/PreReqs/el7/pacemaker-1/
Advanced/RDQM/PreReqs/el7/pacemaker-1/cluster-glue-libs-1.0.12.linbit-3.1+20210204+240ad6b5.el7.x86_64.rpm\r
Advanced/RDQM/PreReqs/el7/pacemaker-1/corosync-2.4.5.linbit-3.0.70.2b5ae51.el7.x86_64.rpm\r
Advanced/RDQM/PreReqs/el7/pacemaker-1/corosynclib-2.4.5.linbit-3.0.70.2b5ae51.el7.x86_64.rpm\r
Advanced/RDQM/PreReqs/el7/pacemaker-1/crmsh-3.0.4.linbit-2.193.799059e2.el7.noarch.rpm\r
Advanced/RDQM/PreReqs/el7/pacemaker-1/crmsh-scripts-3.0.4.linbit-2.193.799059e2.el7.noarch.rpm\r
Advanced/RDQM/PreReqs/el7/pacemaker-1/libqb-1.0.6.linbit-1.1.el7.x86_64.rpm\r
Advanced/RDQM/PreReqs/el7/pacemaker-1/pacemaker-1.1.24.linbit-2.0.el7.x86_64.rpm\r
Advanced/RDQM/PreReqs/el7/pacemaker-1/pacemaker-cli-1.1.24.linbit-2.0.el7.x86_64.rpm\r
Advanced/RDQM/PreReqs/el7/pacemaker-1/pacemaker-cluster-libs-1.1.24.linbit-2.0.el7.x86_64.rpm\r
Advanced/RDQM/PreReqs/el7/pacemaker-1/pacemaker-libs-1.1.24.linbit-2.0.el7.x86_64.rpm\r
Advanced/RDQM/PreReqs/el7/pacemaker-1/python-parallax-1.0.6.linbit-4.0+a257e43.noarch.rpm\r
Advanced/RDQM/PreReqs/el7/pacemaker-1/resource-agents-4.7.0.linbit-0.1.el7.x86_64.rpm\r
Advanced/RDQM/PreReqs/el7/pacemaker-1/\r
Advanced/RDQM/PreReqs/el7/\r
Advanced/RDQM/PreReqs/el8/
Advanced/RDQM/PreReqs/el8/drbd-utils-9/
Advanced/RDQM/PreReqs/el8/drbd-utils-9/drbd-bash-completion-9.27.0-1.el8.x86_64.rpm\r
Advanced/RDQM/PreReqs/el8/drbd-utils-9/drbd-pacemaker-9.27.0-1.el8.x86_64.rpm\r
Advanced/RDQM/PreReqs/el8/drbd-utils-9/drbd-selinux-9.27.0-1.el8.x86_64.rpm\r
Advanced/RDQM/PreReqs/el8/drbd-utils-9/drbd-udev-9.27.0-1.el8.x86_64.rpm\r
Advanced/RDQM/PreReqs/el8/drbd-utils-9/drbd-utils-9.27.0-1.el8.x86_64.rpm\r
Advanced/RDQM/PreReqs/el8/drbd-utils-9/\r
Advanced/RDQM/PreReqs/el8/kmod-drbd-9/
Advanced/RDQM/PreReqs/el8/kmod-drbd-9/kmod-drbd-9.1.22_4.18.0_193-1.x86_64.rpm\r
Advanced/RDQM/PreReqs/el8/kmod-drbd-9/kmod-drbd-9.1.22_4.18.0_240.1.1-1.x86_64.rpm\r
Advanced/RDQM/PreReqs/el8/kmod-drbd-9/kmod-drbd-9.1.22_4.18.0_305-1.x86_64.rpm\r
Advanced/RDQM/PreReqs/el8/kmod-drbd-9/kmod-drbd-9.1.22_4.18.0_348-1.x86_64.rpm\r
Advanced/RDQM/PreReqs/el8/kmod-drbd-9/kmod-drbd-9.1.22_4.18.0_372.9.1-1.x86_64.rpm\r
Advanced/RDQM/PreReqs/el8/kmod-drbd-9/kmod-drbd-9.1.22_4.18.0_425.10.1-1.x86_64.rpm\r
Advanced/RDQM/PreReqs/el8/kmod-drbd-9/kmod-drbd-9.1.22_4.18.0_425.3.1-1.x86_64.rpm\r
Advanced/RDQM/PreReqs/el8/kmod-drbd-9/kmod-drbd-9.1.22_4.18.0_477.10.1-1.x86_64.rpm\r
Advanced/RDQM/PreReqs/el8/kmod-drbd-9/kmod-drbd-9.1.22_4.18.0_513.5.1-1.x86_64.rpm\r
Advanced/RDQM/PreReqs/el8/kmod-drbd-9/kmod-drbd-9.1.22_4.18.0_553-1.x86_64.rpm\r
Advanced/RDQM/PreReqs/el8/kmod-drbd-9/modver\r
Advanced/RDQM/PreReqs/el8/kmod-drbd-9/\r
Advanced/RDQM/PreReqs/el8/pacemaker-2/
Advanced/RDQM/PreReqs/el8/pacemaker-2/cluster-glue-libs-1.0.12.linbit-3.2+20210204+240ad6b5.el8.x86_64.rpm\r
Advanced/RDQM/PreReqs/el8/pacemaker-2/corosync-3.1.0.linbit-2.1.4.4a2f48b1.el8.x86_64.rpm\r
Advanced/RDQM/PreReqs/el8/pacemaker-2/corosynclib-3.1.0.linbit-2.1.4.4a2f48b1.el8.x86_64.rpm\r
Advanced/RDQM/PreReqs/el8/pacemaker-2/crmsh-4.3.0+git.1614332721.c866b885.linbit-1.0.el8.noarch.rpm\r
Advanced/RDQM/PreReqs/el8/pacemaker-2/crmsh-scripts-4.3.0+git.1614332721.c866b885.linbit-1.0.el8.noarch.rpm\r
Advanced/RDQM/PreReqs/el8/pacemaker-2/libknet1-1.20.linbit-1.0.el8.x86_64.rpm\r
Advanced/RDQM/PreReqs/el8/pacemaker-2/libnozzle1-1.20.linbit-1.0.el8.x86_64.rpm\r
Advanced/RDQM/PreReqs/el8/pacemaker-2/libqb-1.0.6.linbit-1.1.el8.x86_64.rpm\r
Advanced/RDQM/PreReqs/el8/pacemaker-2/pacemaker-2.0.5.linbit-1.0.el8.x86_64.rpm\r
Advanced/RDQM/PreReqs/el8/pacemaker-2/pacemaker-cli-2.0.5.linbit-1.0.el8.x86_64.rpm\r
Advanced/RDQM/PreReqs/el8/pacemaker-2/pacemaker-cluster-libs-2.0.5.linbit-1.0.el8.x86_64.rpm\r
Advanced/RDQM/PreReqs/el8/pacemaker-2/pacemaker-libs-2.0.5.linbit-1.0.el8.x86_64.rpm\r
Advanced/RDQM/PreReqs/el8/pacemaker-2/pacemaker-schemas-2.0.5.linbit-1.0.el8.noarch.rpm\r
Advanced/RDQM/PreReqs/el8/pacemaker-2/python3-parallax-1.0.5.linbit-1.0.noarch.rpm\r
Advanced/RDQM/PreReqs/el8/pacemaker-2/resource-agents-4.9.0.linbit-3.0.el8.x86_64.rpm\r
Advanced/RDQM/PreReqs/el8/pacemaker-2/\r
Advanced/RDQM/PreReqs/el8/\r
Advanced/RDQM/PreReqs/el9/
Advanced/RDQM/PreReqs/el9/drbd-utils-9/
Advanced/RDQM/PreReqs/el9/drbd-utils-9/drbd-bash-completion-9.27.0-1.el9.x86_64.rpm\r
Advanced/RDQM/PreReqs/el9/drbd-utils-9/drbd-pacemaker-9.27.0-1.el9.x86_64.rpm\r
Advanced/RDQM/PreReqs/el9/drbd-utils-9/drbd-selinux-9.27.0-1.el9.x86_64.rpm\r
Advanced/RDQM/PreReqs/el9/drbd-utils-9/drbd-udev-9.27.0-1.el9.x86_64.rpm\r
Advanced/RDQM/PreReqs/el9/drbd-utils-9/drbd-utils-9.27.0-1.el9.x86_64.rpm\r
Advanced/RDQM/PreReqs/el9/drbd-utils-9/\r
Advanced/RDQM/PreReqs/el9/kmod-drbd-9/
Advanced/RDQM/PreReqs/el9/kmod-drbd-9/kmod-drbd-9.1.22_5.14.0_162.22.2-1.x86_64.rpm\r
Advanced/RDQM/PreReqs/el9/kmod-drbd-9/kmod-drbd-9.1.22_5.14.0_162.6.1-1.x86_64.rpm\r
Advanced/RDQM/PreReqs/el9/kmod-drbd-9/kmod-drbd-9.1.22_5.14.0_284.11.1-1.x86_64.rpm\r
Advanced/RDQM/PreReqs/el9/kmod-drbd-9/kmod-drbd-9.1.22_5.14.0_284.30.1-1.x86_64.rpm\r
Advanced/RDQM/PreReqs/el9/kmod-drbd-9/kmod-drbd-9.1.22_5.14.0_284.40.1-1.x86_64.rpm\r
Advanced/RDQM/PreReqs/el9/kmod-drbd-9/kmod-drbd-9.1.22_5.14.0_362.13.1-1.x86_64.rpm\r
Advanced/RDQM/PreReqs/el9/kmod-drbd-9/kmod-drbd-9.1.22_5.14.0_362.18.1-1.x86_64.rpm\r
Advanced/RDQM/PreReqs/el9/kmod-drbd-9/kmod-drbd-9.1.22_5.14.0_362.24.1-1.x86_64.rpm\r
Advanced/RDQM/PreReqs/el9/kmod-drbd-9/kmod-drbd-9.1.22_5.14.0_362.8.1-1.x86_64.rpm\r
Advanced/RDQM/PreReqs/el9/kmod-drbd-9/kmod-drbd-9.1.22_5.14.0_427.13.1-1.x86_64.rpm\r
Advanced/RDQM/PreReqs/el9/kmod-drbd-9/kmod-drbd-9.1.22_5.14.0_503.11.1-1.x86_64.rpm\r
Advanced/RDQM/PreReqs/el9/kmod-drbd-9/kmod-drbd-9.1.22_5.14.0_70.13.1-1.x86_64.rpm\r
Advanced/RDQM/PreReqs/el9/kmod-drbd-9/kmod-drbd-9.1.22_5.14.0_70.36.1-1.x86_64.rpm\r
Advanced/RDQM/PreReqs/el9/kmod-drbd-9/modver\r
Advanced/RDQM/PreReqs/el9/kmod-drbd-9/\r
Advanced/RDQM/PreReqs/el9/pacemaker-2/
Advanced/RDQM/PreReqs/el9/pacemaker-2/corosync-3.1.6.linbit-1.0.7.7b96a937.el9.x86_64.rpm\r
Advanced/RDQM/PreReqs/el9/pacemaker-2/corosynclib-3.1.6.linbit-1.0.7.7b96a937.el9.x86_64.rpm\r
Advanced/RDQM/PreReqs/el9/pacemaker-2/crmsh-4.4.0.linbit-2.0.99.311a0a0d.el9.noarch.rpm\r
Advanced/RDQM/PreReqs/el9/pacemaker-2/crmsh-scripts-4.4.0.linbit-2.0.99.311a0a0d.el9.noarch.rpm\r
Advanced/RDQM/PreReqs/el9/pacemaker-2/libknet1-1.24.linbit-1.0.el9.x86_64.rpm\r
Advanced/RDQM/PreReqs/el9/pacemaker-2/libknet1-crypto-nss-plugin-1.24.linbit-1.0.el9.x86_64.rpm\r
Advanced/RDQM/PreReqs/el9/pacemaker-2/libnozzle1-1.24.linbit-1.0.el9.x86_64.rpm\r
Advanced/RDQM/PreReqs/el9/pacemaker-2/libqb-2.0.8.linbit-1.0.el9.x86_64.rpm\r
Advanced/RDQM/PreReqs/el9/pacemaker-2/pacemaker-2.1.2.linbit-4.el9.x86_64.rpm\r
Advanced/RDQM/PreReqs/el9/pacemaker-2/pacemaker-cli-2.1.2.linbit-4.el9.x86_64.rpm\r
Advanced/RDQM/PreReqs/el9/pacemaker-2/pacemaker-cluster-libs-2.1.2.linbit-4.el9.x86_64.rpm\r
Advanced/RDQM/PreReqs/el9/pacemaker-2/pacemaker-libs-2.1.2.linbit-4.el9.x86_64.rpm\r
Advanced/RDQM/PreReqs/el9/pacemaker-2/pacemaker-schemas-2.1.2.linbit-4.el9.noarch.rpm\r
Advanced/RDQM/PreReqs/el9/pacemaker-2/python3-parallax-1.0.6.linbit-9.0+a257e43.noarch.rpm\r
Advanced/RDQM/PreReqs/el9/pacemaker-2/resource-agents-4.13.0.linbit-2.0.33.2667d44c.el9.x86_64.rpm\r
Advanced/RDQM/PreReqs/el9/pacemaker-2/\r
Advanced/RDQM/PreReqs/el9/\r
Advanced/RDQM/PreReqs/\r
repackage/MQSeriesAMQP.spec\r
repackage/MQSeriesAMS.spec\r
repackage/MQSeriesClient.spec\r
repackage/MQSeriesFTAgent.spec\r
repackage/MQSeriesFTLogger.spec\r
repackage/MQSeriesFTService.spec\r
repackage/MQSeriesFTTools.spec\r
repackage/MQSeriesGSKit.spec\r
repackage/MQSeriesJRE.spec\r
repackage/MQSeriesJava.spec\r
repackage/MQSeriesMan.spec\r
repackage/MQSeriesMsg_Zh_CN.spec\r
repackage/MQSeriesMsg_Zh_TW.spec\r
repackage/MQSeriesMsg_cs.spec\r
repackage/MQSeriesMsg_de.spec\r
repackage/MQSeriesMsg_es.spec\r
repackage/MQSeriesMsg_fr.spec\r
repackage/MQSeriesMsg_hu.spec\r
repackage/MQSeriesMsg_it.spec\r
repackage/MQSeriesMsg_ja.spec\r
repackage/MQSeriesMsg_ko.spec\r
repackage/MQSeriesMsg_pl.spec\r
repackage/MQSeriesMsg_pt.spec\r
repackage/MQSeriesMsg_ru.spec\r
repackage/MQSeriesRDQM.spec\r
repackage/MQSeriesRuntime.spec\r
repackage/MQSeriesSDK.spec\r
repackage/MQSeriesSFBridge.spec\r
repackage/MQSeriesSamples.spec\r
repackage/MQSeriesServer.spec\r
repackage/MQSeriesWeb.spec\r
repackage/MQSeriesXRService.spec\r
…………………………………………………………………………………………………………………………………………………………………………………………………………
🛠️  Apply Patch
…………………………………………………………………………………………………………………
Version Before Applying Patch :- 9.2.0.29
…………………………………………………………………………………………………………………
…………………………………………………………………………………………………………………
Executing Command :- rpm --prefix=/opt/mqm -ivh MQSeriesRuntime-* MQSeriesJava* MQSeriesSDK* MQSeriesServer* MQSeriesJRE* MQSeriesSamples* MQSeriesGSKit*
warning: MQSeriesRuntime-U93026-9.3.0-26.x86_64.rpm: Header V4 RSA/SHA256 Signature, key ID 07b22880: NOKEY
Verifying...                          ########################################
Preparing...                          ########################################
Updating / installing...
MQSeriesRuntime-U93026-9.3.0-26       ########################################
MQSeriesGSKit-U93026-9.3.0-26         ########################################
MQSeriesServer-U93026-9.3.0-26        ########################################
MQSeriesJava-U93026-9.3.0-26          ########################################
MQSeriesSDK-U93026-9.3.0-26           ########################################
MQSeriesJRE-U93026-9.3.0-26           ########################################
MQSeriesSamples-U93026-9.3.0-26       ########################################
Command Executed Successfully:- rpm --prefix=/opt/mqm/ -ivh MQSeriesRuntime-* MQSeriesJava* MQSeriesSDK* MQSeriesServer* MQSeriesJRE* MQSeriesSamples* MQSeriesGSKit*
…………………………………………………………………………………………………………………
Version After Patching :- 9.2.0.30
…………………………………………………………………………………………………………………………………………………………………………………………………………
Start Services
…………………………………………………………………………………………………………………
 🚨 Service Name :- TEST1 was in Ended immediately before patching, No need to start service
…………………………………………………………………………………………………………………
 🚨 Service Name :- TEST2 was in Ended immediately before patching, No need to start service
…………………………………………………………………………………………………………………
 🚨 Service Name :- TEST3 was in Ended immediately before patching, No need to start service
…………………………………………………………………………………………………………………
🔔 Service Name :- TEST4 was in Running before patching, proceeding to start service
      ⏳ waiting for service to start
      ⏳ waiting for service to start
      ⏳ waiting for service to start
      ⏳ waiting for service to start
      ⏳ waiting for service to start
      ⏳ waiting for service to start
      ⏳ waiting for service to start
      ⏳ waiting for service to start
  ✅ Service :- TEST4 Status :- Running
…………………………………………………………………………………………………………………
🔔 Service Name :- TEST5 was in Running before patching, proceeding to start service
      ⏳ waiting for service to start
      ⏳ waiting for service to start
      ⏳ waiting for service to start
      ⏳ waiting for service to start
      ⏳ waiting for service to start
      ⏳ waiting for service to start
      ⏳ waiting for service to start
      ⏳ waiting for service to start
  ✅ Service :- TEST5 Status :- Running
…………………………………………………………………………………………………………………………………………………………………………………………………………
End of Patching
===============================================================================
```
---

### 𝐒𝐡𝐞𝐥𝐥 𝐒𝐜𝐫𝐢𝐩𝐭 𝐭𝐨 𝐑𝐨𝐥𝐥𝐛𝐚𝐜𝐤

```bash
#!/usr/bin/env bash

# -----------------------------------------------------------------------------
# Author: Aniket Kakde
# Description: This script performs patching operations on IBM MQ, including
#              pre-requisite checks, backup, patch application, and service management.
# License: Copyright (C) 2025 Aniket Kakde. All rights reserved.
# -----------------------------------------------------------------------------

# declare all variables here
TARGET_USER="mqm"
ROLLBACK_VERSION="9.2.0.30"
echo "==============================================================================="
echo """
                      .-----------------.
                      | IBM MQ Patching |
                      '-----------------'
"""
echo "…………………………………………………………………………………………………………………………………………………………………………………………………………"
echo "➤  Check if Current User is equal to ${TARGET_USER} or not"

current_user=$(whoami)

if [[ "${current_user}" == "${TARGET_USER}" ]]; then
    echo "  ⤷ 👤 Target User :- ${TARGET_USER}"
else
    echo "  ⤷ ❌ Error :- ${TARGET_USER} ≠ ${current_user}"
    exit 1
fi
echo "…………………………………………………………………………………………………………………………………………………………………………………………………………"

if [[ -f "ServiceState.txt" ]]; then
    echo "📋 File ServiceState.txt present"
    echo "…………………………………………………………………………………………………………………"
else
    echo "❌ File ServiceState.txt missing"
    exit 1
    echo "…………………………………………………………………………………………………………………"
fi

FILE_DATA=$(cat "ServiceState.txt")

IFS="#" read -ra Services <<< "$FILE_DATA"

for service in "${Services[@]}"; do

    service_name=$(echo "$service" | awk '{print $1}' | sed 's/QMNAME//g' | sed 's/[()]//g' | awk '{$1=$1; print}')
    service_status=$(echo "$service" | awk '{$1=""; print $0}' | sed 's/STATUS//g' | sed 's/[()]//g' | awk '{$1=$1; print}')
    echo "|-- Service Name → $service_name"
    echo "    ⤷ Service Status → $service_status"

    # only stop services which are in running state
    if [[ "${service_status}" == "Running" ]]; then
        echo "🔔 Service Name :- ${service_name} is in ${service_status}, proceeding to stop service"
        endmqm -i "${service_name}"
        flag=flase
        for((i=0;i<10;i++)){
            temp_service_status=$(dspmq | grep "${service_name}" | awk '{print $2}' | awk '{$1=$1; print}' | sed 's/STATUS//g' | sed 's/[()]//g')
            if [[ "$temp_service_status" != "Running" ]]; then
                flag=true
                break
            fi
            echo "      ⏳ waiting for service to stop"
            sleep 5
        }

        # making sure service is stopped
        get_current_status=$(dspmq | grep "${service_name}" | awk '{print $2}' | awk '{$1=$1; print}' | sed 's/STATUS//g' | sed 's/[()]//g')
        if [[ "$get_current_status" != "Running" ]]; then
            echo "  ✅ Service :- ${service_name} Status :- ${get_current_status}"
        else
            echo "  ❌ Error Service :- ${service_name} Status :- ${get_current_status}, unable to stop service"
            exit 1
        fi
    else
        echo " 🚨 Service Name :- ${service_name} is in ${service_status}, No need to stop service"
    fi
    echo "…………………………………………………………………………………………………………………"
done

# verify if all services are stopped or not
is_any_service_running=false
for service in "${Services[@]}"; do

    service_name=$(echo "$service" | awk '{print $1}' | sed 's/QMNAME//g' | sed 's/[()]//g' | awk '{$1=$1; print}')
    service_status=$(echo "$service" | awk '{$1=""; print $0}' | sed 's/STATUS//g' | sed 's/[()]//g' | awk '{$1=$1; print}')

    service_current_status=$(dspmq | grep "${service_name}" | awk '{print $2}' | awk '{$1=$1; print}' | sed 's/STATUS//g' | sed 's/[()]//g')

    if [[ "$service_current_status" == "Running" ]]; then
        is_any_service_running=true
    fi
done

if [[ "$is_any_service_running" == "true" ]]; then
    echo "❌ Error some services are still running."
    exit 1
fi

echo "🔃 Perform Rollback"
echo "…………………………………………………………………………………………………………………"
version_before_rollback=$(dspmqver | grep "Version:" | awk '{print $2}')
echo "Executing Command :- rpm -qa | grep "$ROLLBACK_VERSION""
rpm -qa | grep "$ROLLBACK_VERSION"
if [[ "$?" == "0" ]]; then
    echo "✅ Command :- rpm -qa | grep "$ROLLBACK_VERSION" Executed Successfully"
else
    echo "❌ Error while executing command :- rpm -qa | grep "$ROLLBACK_VERSION""
fi

echo "…………………………………………………………………………………………………………………"
echo "Executing Command :- rpm -qa | grep "$ROLLBACK_VERSION" | xargs rpm -ev"
rpm -qa | grep "$ROLLBACK_VERSION" | xargs rpm -ev
if [[ "$?" == "0" ]]; then
    echo "✅ Command :- rpm -qa | grep "$ROLLBACK_VERSION" | xargs rpm -ev Executed Successfully"
else
    echo "❌ Error while executing command :- rpm -qa | grep "$ROLLBACK_VERSION" | xargs rpm -ev"
fi

version_after_rollback="9.2.0.28"

echo "…………………………………………………………………………………………………………………"
echo "Version Before Rollback :- ${version_before_rollback}"
echo "Version After Rollback :- ${version_after_rollback}"
echo "…………………………………………………………………………………………………………………………………………………………………………………………………………"

for service in "${Services[@]}"; do

    service_name=$(echo "$service" | awk '{print $1}' | sed 's/QMNAME//g' | sed 's/[()]//g' | awk '{$1=$1; print}')
    service_status=$(echo "$service" | awk '{$1=""; print $0}' | sed 's/STATUS//g' | sed 's/[()]//g' | awk '{$1=$1; print}')
    # only stop services which are in running state
    if [[ "${service_status}" == "Running" ]]; then
        echo "🔔 Service Name :- ${service_name} was in ${service_status} before patching, proceeding to start service"
        strmqm "${service_name}"
        flag=flase
        for((i=0;i<10;i++)){
            temp_service_status=$(dspmq | grep "${service_name}" | awk '{print $2}' | awk '{$1=$1; print}' | sed 's/STATUS//g' | sed 's/[()]//g')
            if [[ "$temp_service_status" == "Running" ]]; then
                flag=true
                break
            fi
            echo "      ⏳ waiting for service to start"
            sleep 5
        }

        # making sure service is stopped
        get_current_status=$(dspmq | grep "${service_name}" | awk '{print $2}' | awk '{$1=$1; print}' | sed 's/STATUS//g' | sed 's/[()]//g')
        if [[ "$get_current_status" == "Running" ]]; then
            echo "  ✅ Service :- ${service_name} Status :- ${get_current_status}"
        else
            echo "  ❌ Error Service :- ${service_name} Status :- ${get_current_status}, unable to stop service"
            exit 1
        fi
    else
        echo " 🚨 Service Name :- ${service_name} was in ${service_status} before patching, No need to stop service"
    fi
    echo "…………………………………………………………………………………………………………………"
done
echo "End of Rollback"
echo "==============================================================================="
```
### 𝐒𝐚𝐦𝐩𝐥𝐞 𝐨𝐮𝐭𝐩𝐮𝐭

```text
===============================================================================

                      .-----------------.
                      | IBM MQ Patching |
                      '-----------------'

…………………………………………………………………………………………………………………………………………………………………………………………………………
➤  Check if Current User is equal to mqm or not
  ⤷ 👤 Target User :- mqm
…………………………………………………………………………………………………………………………………………………………………………………………………………
📋 File ServiceState.txt present
…………………………………………………………………………………………………………………
|-- Service Name → TEST1;
        ⤷ Service Status → Ended immediately
 🚨 Service Name :- TEST1; is in Ended immediately, No need to stop service
…………………………………………………………………………………………………………………
|-- Service Name → TEST2;
        ⤷ Service Status → Ended immediately
 🚨 Service Name :- TEST2; is in Ended immediately, No need to stop service
…………………………………………………………………………………………………………………
|-- Service Name → TEST3;
        ⤷ Service Status → Ended immediately
 🚨 Service Name :- TEST3; is in Ended immediately, No need to stop service
…………………………………………………………………………………………………………………
|-- Service Name → TEST4;
        ⤷ Service Status → Running
🔔 Service Name :- TEST4; is in Running, proceeding to stop service
      ⏳ waiting for service to stop
      ⏳ waiting for service to stop
      ⏳ waiting for service to stop
      ⏳ waiting for service to stop
      ⏳ waiting for service to stop
      ⏳ waiting for service to stop
      ⏳ waiting for service to stop
  ✅ Service :- TEST4; Status :- Ended immediately
…………………………………………………………………………………………………………………
|-- Service Name → TEST5;
        ⤷ Service Status → Running
🔔 Service Name :- TEST5; is in Running, proceeding to stop service
      ⏳ waiting for service to stop
      ⏳ waiting for service to stop
      ⏳ waiting for service to stop
      ⏳ waiting for service to stop
      ⏳ waiting for service to stop
      ⏳ waiting for service to stop
      ⏳ waiting for service to stop
  ✅ Service :- TEST5; Status :- Ended immediately
…………………………………………………………………………………………………………………
🔃 Perform Rollback
…………………………………………………………………………………………………………………
Executing Command :- rpm -qa | grep 9.2.0.30
MQSeriesSamples-U93026-9.3.0-26.x86_64
MQSeriesGSKit-U93026-9.3.0-26.x86_64
MQSeriesJava-U93026-9.3.0-26.x86_64
MQSeriesRuntime-U93026-9.3.0-26.x86_64
MQSeriesSDK-U93026-9.3.0-26.x86_64
MQSeriesJRE-U93026-9.3.0-26.x86_64
MQSeriesServer-U93026-9.3.0-26.x86_64
✅ Command :- rpm -qa | grep 9.2.0.30 Executed Successfully
…………………………………………………………………………………………………………………
Executing Command :- rpm -qa | grep 9.2.0.30 | xargs rpm -ev
Preparing packages...
MQSeriesServer-U93026-9.3.0-26.x86_64
MQSeriesGSKit-U93026-9.3.0-26.x86_64
MQSeriesJRE-U93026-9.3.0-26.x86_64
MQSeriesSDK-U93026-9.3.0-26.x86_64
MQSeriesJava-U93026-9.3.0-26.x86_64
MQSeriesSamples-U93026-9.3.0-26.x86_64
MQSeriesRuntime-U93026-9.3.0-26.x86_64
✅ Command :- rpm -qa | grep 9.2.0.30 | xargs rpm -ev Executed Successfully
…………………………………………………………………………………………………………………
Version Before Rollback :- 9.2.0.30
Version After Rollback :- 9.2.0.28
…………………………………………………………………………………………………………………………………………………………………………………………………………
 🚨 Service Name :- TEST1; was in Ended immediately before patching, No need to stop service
…………………………………………………………………………………………………………………
 🚨 Service Name :- TEST2; was in Ended immediately before patching, No need to stop service
…………………………………………………………………………………………………………………
 🚨 Service Name :- TEST3; was in Ended immediately before patching, No need to stop service
…………………………………………………………………………………………………………………
🔔 Service Name :- TEST4; was in Running before patching, proceeding to start service
      ⏳ waiting for service to start
      ⏳ waiting for service to start
      ⏳ waiting for service to start
      ⏳ waiting for service to start
      ⏳ waiting for service to start
      ⏳ waiting for service to start
      ⏳ waiting for service to start
  ✅ Service :- TEST4; Status :- Running
…………………………………………………………………………………………………………………
🔔 Service Name :- TEST5; was in Running before patching, proceeding to start service
      ⏳ waiting for service to start
      ⏳ waiting for service to start
      ⏳ waiting for service to start
      ⏳ waiting for service to start
      ⏳ waiting for service to start
      ⏳ waiting for service to start
      ⏳ waiting for service to start
  ✅ Service :- TEST5; Status :- Running
…………………………………………………………………………………………………………………
End of Rollback
===============================================================================
```

### 𝐀𝐝𝐯𝐚𝐧𝐭𝐚𝐠𝐞𝐬 𝐨𝐟 𝐚𝐮𝐭𝐨𝐦𝐚𝐭𝐢𝐨𝐧 𝐭𝐡𝐚𝐭 𝐡𝐚𝐝 𝐨𝐯𝐞𝐫 𝐦𝐚𝐧𝐮𝐚𝐥 𝐩𝐫𝐨𝐜𝐞𝐬𝐬

1. **Time Efficiency:** Cuts patching time from 30 minutes to 10 minutes per system.
---
2. **Resource Dependency:** Lowers the need for senior resources.
---
3. **Resource Savings:** Allows for parallel patching, saving both time and resources.
---
4. **Error Reduction:** Reduces human errors in patching tasks.
---
5. **Consistent Standards:** Maintains uniform standards across operations.
---
6. **Improved Logging:** Offers detailed and consistent logging.
---
![Advantages Infograph](/Advantages-without-back.png)
---

> 💻 ⚙️ 𝐓𝐨𝐨𝐥𝐬 𝐔𝐬𝐞𝐝   
[𝟏. 𝐈𝐁𝐌 𝐌𝐐 (𝐈𝐁𝐌 𝐖𝐞𝐛𝐬𝐩𝐡𝐞𝐫𝐞)](https://www.ibm.com/products/mq)   
[𝟐. 𝐒𝐕𝐍 (𝐬𝐮𝐛𝐯𝐞𝐫𝐬𝐢𝐨𝐧)](https://subversion.apache.org/)   
[𝟑. 𝐈𝐁𝐌 𝐔𝐫𝐛𝐚𝐧𝐂𝐨𝐝𝐞 𝐃𝐞𝐩𝐥𝐨𝐲 (𝐈𝐁𝐌 𝐔𝐃𝐞𝐩𝐥𝐨𝐲/ 𝐈𝐁𝐌 𝐃𝐞𝐯𝐎𝐩𝐬 𝐃𝐞𝐩𝐥𝐨𝐲)](https://www.ibm.com/products/devops-deploy)

---

![CC BY 4.0](https://img.shields.io/badge/License-CC%20BY%204.0-lightgrey.svg)

This work is licensed under a [Creative Commons Attribution 4.0 International License](http://creativecommons.org/licenses/by/4.0/).
