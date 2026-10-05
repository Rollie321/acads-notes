# Process

### Encoding/Source
* starts from browser, where user types in  searchbar
### Transmitter
- browser starts encoding request object with additional header and meta data
- convert req to bin
### Channel
-  send bin as radio wave to wifi
- wifi router antenna detects radio wave and converts it back to electric signals
- electric signals travel through copper wires to reach modem
* electric signals are converted to light to travel in fiber optics to reach ISP
- ISP sends request to target server
### Decoding
- server recieves light signal and conver it back to binary
- server performs logics to create the matching response to the request object
* response is converted to binary, and travels as light again to travel in fiber optics.
### Destination
* the process happens in reverse to travel from server to client
- light -> electricity -> radio -> binary -> human readable


# Noise
### Physical
- Faulty cables
- anchors from ships hitting optic fiber under ocean
- faulty device making improper request

### Digital
- reaching request limits on load balancer
- electromagnetic interference
- Multipath fading
- Crosstalk
- SAMPLE SITUATION
    - Broken config that crashed cloudflre on november 2025