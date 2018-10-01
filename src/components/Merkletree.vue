<template>
  <div id="app">
      <br> <br>
      <p class="title"> MerkleTree </p>
      <br> <br>
    <br>
      <ul>
        <li v-for="branch in allBranches">
          <p v-if="branch.length==1">
            The merkleroot is {{merkleroot}}
          </p>
          <ul>
            <p v-if="branch.length>1 && branch.length%2 != 0"> 
              Cannot build Merkle Tree for odd number of data elements. <br> 
              Duplicating the last transaction to achieve an even number of data elements. <br>
            </p>
            <li v-for="tx in branch">
              <md-card md-with-hover class="hashCards"> 
                {{tx}}
              </md-card>
            </li> <br>
          </ul> <br>
        </li>
      </ul>
      <ul>
        <li v-for="tx in hashedtxs">
          <md-card md-with-hover class="hashCards"> 
            {{tx}}
          </md-card> 
        </li> <br>
        <p v-if="hashedtxs.length!=0">
          Leaf nodes hashed using double-SHA256 algorithm
        </p>

      </ul>
      <ul v-if="txs.length>1 && txs.length%2!=0">
        <li v-for="tx in txsCopy">
          <md-card md-with-hover class="txCards"> 
            {{tx}}
          </md-card>
        </li> <br>
        <p>
          Cannot build Merkle Tree for odd number of data elements. <br> 
          Duplicating the last transaction to achieve an even number of data elements.
        </p>
      </ul>
      <ul>
        <li v-for="tx in txs" v-if="txs.length!=0">
          <p v-if="txs.length==1">
            {{message}} <br>
          </p> 
          <md-card md-with-hover class="txCards"> 
            {{tx}}
          </md-card> 
        </li> <br>
        <p v-if="txs.length!=0">
          Transactions to be summarized
        </p>
      </ul>
      <span class="input">
      <ul>
        <li>
          <p> Enter the number of transactions to be summarized <br> </p>
          <md-field>
            <md-input ref="n" id="n" v-model="n" @keyup.enter.native="getN()" placeholder="The value of n"></md-input>
          </md-field>
          <p v-if="n!=0 && n>1">Enter the transaction IDs separated by comma        <br> </p>
          <md-field  v-if="n!=0 && n>1">
            <md-input ref="tx" id="tx" v-model="tx" @keyup.enter.native="getTx()" placeholder="Tx IDs separated by comma"></md-input>
          </md-field>
        </li>
      </ul>
      </span>
      <!--<p class="subtitle" v-if="txs.length==0">
        There are no transactions to be summarized
      </p> <br> -->
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
    //await this.getN();
    //await this.getTx();
    //await this.hashTxs();
    //await this.calcBranches();
  },
  methods: {
    async getN() {
      this.n = parseInt(document.getElementById("n").value)
      console.log(this.n);
    },
    async getTx() {
      this.txs=[];
      this.tx = document.getElementById("tx").value;
      this.txs = this.tx.split(",")
      if(this.txs.length != this.n) {
        console.log("Number of transactions does not match the value of n")
      }
      console.log(this.txs);
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
    hashDupTxs() {
    }
  }
}
</script>

<style>
.txCss {
  text-align: center;
  border-radius: 2px;
}
.txCards {
  display: inline-block;
  height: 45px;
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
  height: 135px;
  width: 120px;
  overflow: visible;
  border-radius: 2px;
  text-align: center;
  color: #0c2636;
  margin-top:0;
  margin: 10px;
  vertical-align: middle;
  padding:10px;
}
.arrowCards {
  margin-top: 100px;
  display: inline-block; 
  width: 100px;
  border-radius: 2px;
  margin: 90px;
  position: initial;
}
.leaves {
  width: 50px;
  height: 50px;
  text-align: center;
}
#app ul {
  list-style: none;
  horizontal-align: center;
  -webkit-padding-start: 0px;
}
.title{
  font-size: 40px;
}
#app li {
  display: inline;
  horizontal-align: center;
}
p{
  display: inline;
  text-align: left;
}
.input {
  width: 500px;
  margin: 3px;
  display: inline-block;
  vertical-align: center;
  color:#2C3539;
}
.md-field {
  padding-left: 80px;
  text-align:right;
  width: 100%;
  display:inline-block;
}
</style>
