# p4register
Sample P4 program to show the usage of registers.
In particular, in this application a simple TCP netcat communication is assumed on port 4444 (see the parser for more details).
If one character is sent by the client to the server, the application replaces that character with another one found/already pushed to the register. The original character, then, will update the register, therefore the server will always be behind the client with one character.

# Example
| Client@10.0.0.1                       | Server@10.0.0.2:4444                |
| ------------------------------------- |------------------------------------ |
|`[CLIENT]root #  netcat 10.0.0.2 4444` | `[SERVER] root # netcat -l -p 4444` |  
|`a`                                    | ` `                                 |
|`s`                                    | `a`                                 |
|`d`                                    | `s`                                 |
|` `                                    | `d`                                 |

# Simplified state machine
 - TCP payload, or at least 2 bytes in the beginning of it is being parsed as well
 - TCP payload length is calculated when TCP is parsed
    - if it is 0, nothing happens just port forwarding between the two ports of the switch
    - otherwise, we continue parsing
      - if macskafasz
 
 - modifies the TCP payload
