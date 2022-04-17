<template>
  <v-container>
    <div v-if="gameSetup">
    <v-row class="text-center">
      <v-col cols="10">
          <v-text-field
            label="Room Name"
            v-model="myid"
          ></v-text-field>
      </v-col>
      <v-col cols="2">
        <v-btn
          color="primary"
          dark
          large
          @click="SetGame"
        >
          Start Game
        </v-btn>
      </v-col>
    </v-row>
    <v-row>
      <v-col cols="10">
        <v-text-field
          label="Room Name"
          v-model="gameHost"
        ></v-text-field>
      </v-col>
      <v-col cols="2">
        <v-btn
          color="primary"
          dark
          large
          @click="joinGame"
        >
          Join Game
        </v-btn>
      </v-col>
    </v-row>
    </div>
    <div v-if="!gameSetup">
      <v-row>
        <v-col cols="12">
          <p> Room Name: {{gameHost}} </p>
        </v-col>
      </v-row>
    <v-btn
      color="black"
      dark
      large
    >
    {{randBlackCard.text}}
    </v-btn>
    <!--- HOST -->
    <div v-if="myid === gameHost">
    <v-btn
      v-for="(card, index) in Answers"
      :key="index"
      @click="hostSend(Answers[index]['sender'], {status:'winner'})"
    >
      {{card.text}}
    </v-btn>
    </div>
    <!--- JOIN -->
    <div v-if="myid !== gameHost">
    <v-btn
      v-for="(card, index) in randWhiteCards"
      :key="index"
      class="white-card"
      @click="send(randWhiteCards[index])"
    >
      {{card.text}}
    </v-btn>
    <v-row v-if="win"><br><br>
      🎉WINNER!!🎉
    </v-row>
    </div>
    </div>
  </v-container>
</template>

<script>
  import Peer from 'peerjs';
  export default {
    name: 'GamePage',
    data () {
      return {
        myid: null,
        gameHost: null,
        gameSetup: true,
        win: false,
        connections: {},
        newData: null,
        opponent: null,
        randBlackCard: null,
        Answers: [],
        randWhiteCards: [],
        blackCards: [
          {card: 1, text:"I got 99 problems but _ ain't one."},
          {card: 2, text: "When I am president, I will create the Department of _."},
          {card: 3, text: "What's the one thing I can never do? _."},
          {card: 4, text: "What never fails to liven up the party? _."},
          {card: 5, text: "What gets better with age? _."}
        ],
        whiteCards: [
          {card: 1, text: "Exactly what you'd expect."},
          {card: 2, text: "Cuddling"},
          {card: 3, text: "A good sniff."},
          {card: 4, text: "Old-people smell"},
          {card: 5, text: "JD Power and his associates"},
          {card: 6, text: "Emotions"},
          {card: 7, text: "A zesty breakfast burrito."},
          {card: 8, text: "The Force."},
          {card: 9, text: "Jobs"},
          {card: 10, text: "Getting crushed by a vending machine"},
          {card: 11, text: "Centaurs"},
          {card: 12, text: "Nickelback."},
        ]    
      };
    },
    mounted() {},
    created () {
        this.myself = new Peer(); //STUN and TURN servers get passed into here
        // If no STUN or TURN servers specified, default is a public STUN and TURN servers
        this.myself.on('open', id => {
          this.myid = id;
          console.log('My Peer ID is: ' + id);
        });
        if (this.myid === this.gameHost) {
      var black = Math.floor(Math.random() * 5) + 1;
      this.randBlackCard = this.blackCards[black-1];
        }
      // add 5 white cards to randWhiteCards array
      for (var i = 0; i < 5; i++) {
        var white = Math.floor(Math.random() * 12) + 1;
        this.randWhiteCards.push(this.whiteCards[white-1]);
      }
    },
    methods: {
      SetGame () {
        // Host
        this.gameSetup = false;
        this.gameHost = this.myid;
        this.receive(this.randBlackCard);
      },
      joinGame () {
        // Peer
        this.gameSetup = false;
        this.connect(this.gameHost, this.myid);
      },
      connect(opponent, myid){
        // Peers
        this.opponent = opponent;
      var conn = this.myself.connect(opponent);
      conn.on('open', () => {
        conn.send(myid);
        conn.on("data", (data) => {
          console.log("data", data);
          if (data["black"]) {
            this.randBlackCard = data["black"];
          }
        });
      });
      },
      hostSend(conn, data) {
        console.log("hostSend", conn, data);
        console.log(this.connections[conn]);
        this.connections[conn].send(data);
      },
      receive (black_card) {
        // Host
        this.myself.on('connection', (conn) => {
          conn.on('data', (data) => {
            console.log("peer", data);
            if (data["white"]) {
              var white = data["white"];
              white['sender'] = conn.peer;
              this.Answers.push(white);
            }
            this.connections[conn.peer] = conn;
            conn.send({"black": black_card, "ID": this.myid});
          });
        });
      },
      send (message) {
        // Peer
        console.log("send", {"white": message, "ID": this.myid});
        var conn = this.myself.connect(this.opponent);
        conn.on('open', () => {
          conn.send({"white": message, "ID": this.myid});
        });
        //remove card from array and add a new random card
        this.randWhiteCards.splice(this.randWhiteCards.indexOf(message), 1);
        var white = Math.floor(Math.random() * 12) + 1;
        this.randWhiteCards.push(this.whiteCards[white-1]);
        conn.on("data", (data) => {
          console.log("data", data);
          if (data["status"] === "winner") {
            console.log("Winner!!!");
            this.win = true;
          }
        });
      }
    },
  }
</script>
