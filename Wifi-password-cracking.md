# Filpper Zero+Marauder Wifi Cracking Guide

0) Scroll to the bottom in marauder and enable saving to flipper sd card
1) Scan for APs (wifis)
2) Aelect target ap
2) Sniff->PMKID->Targeted Active (taking note of the file name)
2.5) give it a good five minutes to do its thing
3) Transfer PCAP file onto computer with Qflipper
4) The file will be at SD Card->apps_data->marauder->pcaps
5) Upload the file to this link and convert https://hashcat.net/cap2hashcat/ 
6) Download the converted file (the .hc22000 file)

## Password cracking methodologies
#### Easiest: online hash cracking services
You can upload your converted file to these sites and they will try to crack it for you. Keep in mind that while these services are often effective, these are best used as an easy way to pick the low hanging fruit.
* https://www.onlinehashcrack.com/dashboard
* https://wpa-sec.stanev.org/?submit
* https://banthex.de

## Going hardcore, or, "This password wont crack"
Manually cracking hashes is far more powerful and likely to work, but it requires installing the cracking software (Hashcat) and you'll want the beefiest GPU you have access too because the power the GPU makes all the difference.

**Install Hashcat**
1) Download and install hashcat from here https://hashcat.net/hashcat/
2) In the folder you extracted hashcat to, put the .hc22000 file in that folder next to hashcat.exe

#### Dictionary attacks
A dictionary attack is where you use a wordlist and try each line against the hash. There are countless wordlists and they all have their pros and cons.  
Note that hashcat can accept plain .txt files but also compressed .tar.gz and .gz files without extracting them.  
Another thing to keep in mind is that some wordlists can be absolutely huge, so they take up lots of space and take much longer to run The tradeoff being that they're more likely to crack the hash.

1) Download a wordlist
    * rockyou.txt.gz is the usual goto classic one https://github.com/praetorian-inc/Hob0Rules/tree/master/wordlists
        - **NOTE:** Do not bother downloading or using "rockyou2024" its a terrible wordlist, its near impossible to work with, and the actual wordlist is completely broken and basically wont work
    * Crackstation wordlist is a community favorite https://crackstation.net/crackstation-wordlist-password-cracking-dictionary.htm
    * Assorted common wordlists https://github.com/gmelodie/awesome-wordlists?tab=readme-ov-file#passwords
    * More assorted wordlists https://github.com/kkrypt0nn/wordlists/tree/main/wordlists/passwords
    * My personal (massive) wordlist made up of every leaked, breached, and cracked password I could get my hands on, plus some languages dictionaries and some generated passwords https://mega.nz/file/1w11iA7I#q-SrKp4BBtgEbI-fJorYCCsA1CYXKniKwubqj-L46yo
2) Put that .txt.gz file in the same folder next to your .hc22000 and hashcat.exe file. No need to extract it.
3) Right click on an empty space in the folder and click "Open in Terminal"
4) run this command, replace <wordlist filename> and <.hc22000 filename> `-a 0` tells hashcat to use attack mode 0 (dictionary attack) `--status` just makes it update the terminal window during the attack
5) `.\hashcat -a 0 --status <.hc22000 filename> <wordlist filename>`
6) exaple: `.\hashcat -a 0 pcap0.hc22000 rockyou.txt.gz`

#### Rules based attacks
Rules based attacks are broadly the very most effective type of attack. The tradeoff is that they can be very, very slow to run.  
The general idea is that you will have two files: a wordlist and a rules file. The wordlist should be optimized for rules based attacks, generally made by appending a normal dictionary to a good (but ideally smaller) wordlist. 
This is my personal rules based attack wordlist, [princesspi_rules_dictionary_2.0.tar.gz](https://mega.nz/file/B5VBXKSQ#i4guRZCdfpzWvrhP4H2o84tTcb7PmqYjPve6n1JNdWQ)
As for rules files, there are a few good ones out there:  
* Probably the very best: https://github.com/stealthsploit/OneRuleToRuleThemStill
* https://github.com/Unic0rn28/hashcat-rules/tree/main
* https://github.com/n0kovo/hashcat-rules-collection

To run a rules based attack the command looks like:
`.\hashcat -a 0 --status <.hc22000 filename> <wordlist filename> -r <rules filename> --debug-mode=1 --debug=file=<your choice debug file name>.rule --force`
`-a 0` means attack mode 0 (dictionary)
`--status` just makes it update the terminal window during the attack
`-r rules` tells hashcat that its a rules based attack
`--debug-mode=1` tells hashcat that we want it to output extra information, itwill let us generate a file that contains the specific rule(s) that worked
`--debug=file=<your choice debug file name>.rule` is the filename of the outputted rule that matched, makes the filename end in .rule
`--force` just tells hashcat to keep going even if there are errors
Example: `.\hashcat -a 0 --status pcap0.hc22000 princesspi_rules_dictionary_2.0.tar.gz -r OneRuleToRuleThemStill.rule --debug-mode=1 --debug=file=pcap_rules_or_whatever.rule --force`


## Password Cracking Theory 101
The pcap file does NOT contain the password it contains a digest of the password that can not be directly converted into a password. (its like turning a chicken mcnugget back into a chicken). All you can do is try millions of passwords against it, digesting each one with the same algorithm and seeing if they match. if the digest and your file match, the word or password that was used to generate that digest must be the password.  
  
The only way to do this functionally is by comprehensively trying billions of passwords, a, a1, a2, a3,etc OR trying a long list of common passwords (I like rockyou.txt) and hoping to get lucky.  

One powerful way to do it is to use a good wordlist like an English dictionary+rockyou.txt put into one text file and trying rules based attacks with Hashcat, I like hoborules and one rule to rule them all.  
  
Problem: this process is nightmarishly hard, its slow and its hard for a computer to do. that is why hashcat runs on your GPU. You can not crack the password on your flipper, you need a  
  
Ideally, you'd have a decent graphics card on your computer to run hashcat with. there is no way to do this on the flipper or wifi dev board, this is simply too intensive a computing task to do on such a small, low power nugget.