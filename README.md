# p4register
Sample P4 program to show the usage of registers.
In particular, in this application a simple TCP netcat communication is assumed on port 4444 (see the parser for more details).
If one character is sent by the client to the server, the application replaces that character with another one found/already pushed to the register. The original character, then, will update the register, therefore the server became always be 'late' with one character.

# Example
| Client@10.0.0.1                 | Server@10.0.0.2:4444         |
| ------------------------------- |------------------------------|
|`client $  netcat 10.0.0.2 4444` | `server $ netcat -l -p 4444` |                              

