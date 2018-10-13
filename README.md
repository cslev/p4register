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
      - if tcp.dstPort is 4444, then it is our netcat communication, so we do extract those 2 bytes from the beginning of the payload
      - otherwise, just port forwarding between the two ports of the switch
 - Packet headers are sent through different debugging tables to provide a lot of output on the console and make the process easily followable (adapted from my other P4 example code: [p4debug](https://github.com/cslev/p4debug) - verbosity can be controller by the #defines in the beginning of the source code)
 - in case we have one character caught from the netcat communication, we first read the registers value (empty character in the beginning), write that value into the payload, and update the register with the character caught from the communication.
 - Since, we modify the TCP payload, we need to recalculate the TCP checksum (this is why we have `meta.tcp_metadata`, `meta.modified`, etc.). Note that IPv4 Checksum does not need to be recalculated as we do not do any changes in that header (e.g., don't decrease TTL, don't change MAC address). See the MyComputeChecksum() for more details about the TCP checksum calculation (for  brevity, I did not use the incremental TCP checksum calculation, so each time it is recalculated from scratch - but it's OK, don't worry :)).
 
 # Compilation
 ```
 # p4c-bm2-ss registers.p4 -o registers.json
 ```
 
 # Execution
 ```
 # simple_switch --log-console -i 0@eno3 -i 1@eno4 registers.json
 ```
 Note that my two interfaces were `eno3` and `eno4`, and don't forget the `--log-console` to have the debugging 'print-outs'.
 
