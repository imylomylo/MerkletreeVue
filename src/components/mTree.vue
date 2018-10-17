<template>
  <div id="app">
    <div id="header">
      <div class="md-title"> Merkletree </div>
    </div> <br> <br>
    <div id="tree">
      <ul>
        <li v-for="branch in allBranches">
          <p v-if="branch.length==1">
            The merkleroot is {{merkleroot}} <br> <br>
          </p>
          <ul>
            <p v-if="branch.length>1 && branch.length%2 != 0">
                        <br>
          <md-icon>keyboard_arrow_up</md-icon>
              <br> 
              Cannot build Merkle Tree for odd number of data elements. <br> 
              Duplicating the last transaction to achieve an even number of data elements. <br> <br>
            </p>
            <li v-for="tx in branch">
              <md-card md-with-hover class="hashCards"> 
                {{tx}}
              </md-card>
            </li>
          </ul>
        </li>
      </ul>
      <ul>
        <li v-for="tx in hashedtxs">
          <md-card md-with-hover class="hashCards"> 
            {{tx}}
          </md-card> 
        </li> <br>
        <p v-if="hashedtxs.length!=0"> 
          <br>
          <md-icon>keyboard_arrow_up</md-icon>
          <br>
          Leaf nodes hashed using double-SHA256 algorithm
        </p>
      </ul>
      <ul v-if="txs.length>1 && txs.length%2!=0">
        <li v-for="tx in txsCopy">
          <md-card md-with-hover class="hashCards"> 
            {{tx}}
          </md-card>
        </li> <br>
        <p> 
          <br>
          <md-icon>keyboard_arrow_up</md-icon>
          <br>
          Cannot build Merkle Tree for odd number of data elements. <br> 
          Duplicating the last transaction to achieve an even number of data elements.
        </p>
      </ul>
      <ul>
        <li v-for="tx in txs" v-if="txs.length!=0">
          <p v-if="txs.length==1">
            {{message}} <br>
          </p> 
        </li>
      </ul>
    </div>
    <p v-if="n>0"> 2. Enter the transaction IDs <br>
      <md-card class="hashCards" v-for="value in n" :key="value">
        <md-field class="txField">
          <label>Tx ID {{value}}</label>
          <md-input :id="'tx'+value"
            v-on:change="getTx()"
            placeholder="Tx ID"></md-input>  <br>
        </md-field>
      </md-card>
    </p>
    <br><br>

    <p class="inputN"> 1. Enter the number of transactions <br>
      <md-field class="nField">
        <label class="big">The value of n</label>
        <md-input id="n" v-model="n" type="number" v-on:change="getN()" placeholder="n"></md-input>
      </md-field>
    </p>
    <br><br><br>

  </div>
</template>

<script>
const sha256 = require("js-sha256").sha256
export default {
  name: 'MerkleTree',
  data () {
    return {
      n: "", // number of transactions
      tx: "", // transaction ID
      arr: 1,
      ts: new Date(),
      txs: [
        //list of transaction IDs
        /*"tx1",
        "tx2",
        "tx3",
        "tx4",
        "tx5",
        "tx6",
        "tx7",
        "tx8",
        "tx9",
        "tx10",
        "tx11",*/
      ],
      txsCopy: [
        /*copy of list of transactions
        "tx1",
        "tx2",
        "tx3",
        "tx4",
        "tx5",
        "tx6",
        "tx7",
        "tx8",
        "tx9",
        "tx10",
        "tx11",*/
      ],
      hashedtxs: [
        //list of transactions hashed using double-SHA algorithm
        //aka leaf nodes
      ], 
      allBranches: [
        //[
        //array containing all the corrected branches
        //],
      ],
      branch: [
        //list of hashes obtained by concatenating two child nodes and hashing them with double-SHA algorithm
      ],
      merkleroot: '', //alphanumeric string
      message: '', //string 
    }
  },
  async created() {
  },
  methods: {
    async getN() {
      this.n = parseInt(document.getElementById("n").value)
      if(this.n==0) {
        alert("There are no transactions to be summarized");
      } else if(this.n<0) {
        alert("Invalid input")
      }
    },
    async getTx() {
      this.txs=[];
      for(let i=1; i<=this.n; i++) {
        let tx = document.getElementById("tx"+i).value;
        this.txs.push(tx);
      }
      this.hashTxs();
      this.calcBranches();
    },
    async hashTxs() {
      //hash the transactions and display the leaf nodes
      if (this.txs.length == 1) {
        this.merkleroot = sha256(sha256(this.txs[0]))
        this.message = `The Merkle Root is ${this.merkleroot}`  
      }
      else if (this.txs.length > 1){
        this.txsCopy = [];
        this.hashedtxs = [];
        this.message = `Transactions to be summarized: ${this.txs}`
        this.txsCopy = Array.from(this.txs)
        this.makeElementsEven(this.txsCopy) 
        for (const tx of this.txsCopy) {
          this.hashedtxs.push(sha256(sha256(tx)))
        }
      }
    },
    async calcBranches() {
      //calculates the first branch by concatenating n & n+1 nodes from hashedtxs[] array.
        //pushes the resulting branch[] to allBranches[[], [], []] array
      //checks whether the resulting branch[] has even or odd number of elements
      //if odd, clones that branch to a new Array oddBranch[]
      //then, makes the branch[] array even by duplicating the last element
      //calculates the next branch

      const repeat = Math.ceil(Math.log2(this.txsCopy.length))
      this.allBranches = [];
      this.branch = [];
      for(let i =0; i<=repeat; i++) {
        if(this.branch.length == 0) {
          this.hashedtxs.forEach((item, index) => {
            if (index % 2 == 0) {
              this.branch.push(
                sha256(sha256(this.hashedtxs[index].concat(this.hashedtxs[index + 1])))
              )
            }
          }) 
          this.allBranches.unshift(this.branch)
          if(this.branch.length>1 && this.branch.length%2 != 0) {
            let index = this.allBranches.indexOf(this.branch)
            let oddBranch = this.branch.slice(0)
            this.makeElementsEven(this.branch)
            this.allBranches.splice(index+1, 0, oddBranch);
          }
        } 
        else if(this.branch.length>1 && this.branch.length %2 ==0) {
          let branchCopy = this.branch.slice(0)
          this.branch = [];
          branchCopy.forEach((item, index) => {
            if(index%2 ==0){
              this.branch.push(
                sha256(sha256(branchCopy[index].concat(branchCopy[index+1])))
              )
            }
          })
          this.allBranches.unshift(this.branch) 
          if(this.branch.length>1 && this.branch.length%2 != 0) {
            let index = this.allBranches.indexOf(this.branch)
            let oddBranch = this.branch.slice(0)
            this.makeElementsEven(this.branch)
            this.allBranches.splice(index+1, 0, oddBranch);
          }
        }
        else if(this.branch.length==1) {
          this.merkleroot = this.branch[0];
          console.log(`The merkleroot is ${this.merkleroot}`)
        }
      }
    },
    makeElementsEven(arr) {
      if (arr.length > 1 && arr.length % 2 != 0) {
        arr.push(arr[arr.length - 1])
      }
    },
  }
}
</script>

<style scoped>
@import url('https://fonts.googleapis.com/css?family=Lato');

#app {
  margin-top: -20px;
  text-align: center;
  height: 960px;
  overflow: auto;
  font-family: 'Lato', sans-serif; 
}
#header {
  margin-top: 40px;
}
.md-title {
  font-size:2vw;
  letter-spacing: -3px; 
}
.md-input {
  text-align: left;
  overflow: scroll;
}
.nField {
  width: 20%;
  margin: auto;
}
.inputCard {
  width: 40%;
  margin: auto;
}
.txField {
  padding: 25px;
}
.md-button {

}
.txCss {
  text-align: center;
  border-radius: 2px;
}
.txCards {
  display: inline-block;
  height: 50px;
  width: 50px;
  border-radius: 2px;
  text-align: center;
  color: #0c2636;
  margin: 10px;
  vertical-align: middle;
  line-height: 45px;
}
.hashCards {
  word-wrap: break-word;
  display: inline-block;
  height: 120px;
  width: 150px;
  overflow: visible;
  border-radius: 2px;
  text-align: center;
  color: #0c2636;
  margin-top:0;
  margin: 10px;
  vertical-align: middle;
  padding:10px;
}
p{
  display: inline;
  text-align: left;
}
</style>
