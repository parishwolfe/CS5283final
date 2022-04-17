# CS5283final

Cards In Opposition Of Mankind is a clone of a popular card game. This game uses the webRTC protocol to communicate game data peer to peer while the webserver takes care of the signaling. WebRTC is often used for audio/video feeds in many popular videoconfrencing applications. This app uses the datachannel feature of the protocol to communicate. 

## Usage

To bring up the app locally run  
`npm install`  
`npm run serve`  

This will bring the site live on `http://localhost:8080`  

once the site is up, open multiple browsers and go to the site, the host clicks "start" and all others copy the host's Room Number and paste it into the 2nd box, then click "join." This will start the game. The host picks the winner. All players will be shown the same black card. Non-host players will select a white card that they feel fits the black card. When a white card is selected, it is sent to the host. The host can select a winner at any time. When a winner is selected, a "win" message will be displayed on the non-host player's screen. To play again, refresh the page and repeat the process.  
Automatic renewal of the game upon a selected winner is not implemented. 

## More on WebRTC

This application uese STUN and TURN servers provided by the peerJS package. When a player is on a different network, behind a NAT, communication is routed through these servers to allow for communication behind the NAT. 
