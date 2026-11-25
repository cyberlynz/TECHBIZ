# Week 6 · Task 2 — Password Cracking (Hashes)

## Overview

For this task, I worked on password cracking by analysing the supplied password-hash material and using the tools documented in my report to test it against a wordlist.

## What I worked on

I followed the hash-identification and cracking process recorded in the report. This included preparing the input for the cracking tool, selecting the appropriate wordlist, running the cracking attempt, and checking the output to determine whether a match had been found.

Where an encrypted SSH private key was involved, I used `ssh2john` to convert it into a format that John the Ripper could process before carrying out the wordlist test.

## Evidence and results

The report contains the commands, terminal output, and screenshots captured during the exercise. The evidence records the cracking process and the result obtained in the lab rather than relying on an assumed password or an unverified result.

## What I learned

This task helped me understand that password cracking depends on the format of the input, the quality of the wordlist, and the time available for testing. I also got more practice preparing password material for John the Ripper and interpreting its output.
