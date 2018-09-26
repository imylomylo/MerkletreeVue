<template>
  <div id="app">
    <md-card class="txCss">
      <br>
      <ul>
        <li v-for="(branch, key) in allBranches" :key="key">
          {{key}} : {{branch}} <br> <br>
        </li>
      </ul>

      <ul>
        <li v-for="tx in hashedtxs">
          <md-card md-with-hover class="hashCards"> 
            {{tx}}
          </md-card>
        </li>
      </ul>
      <!--<ul>
        <li v-for="tx in branches">
          <md-card md-with-hover class="hashCards"> 
            {{tx}}
          </md-card>
        </li>
      </ul>
      <p>
        3. Leaf nodes hashed using double-SHA algorithm
      </p>-->
      <p>
        3. Leaf nodes hashed using double-SHA algorithm
      </p>
      <ul v-if="txs.length%2!=0">
        <li v-for="tx in txsCopy">
          <md-card md-with-hover class="txCards"> 
            {{tx}}
          </md-card>
        </li>
      </ul>
      <p>
        2. Cannot build Merkle Tree for odd number of data elements. <br> 
        Duplicating the last transaction to achieve an even number of data elements.
      </p>
      <ul>
        <li v-for="tx in txs">
          <md-card md-with-hover class="txCards"> 
            {{tx}}
          </md-card>
        </li>
      </ul>
      <p>
        1. List of transactions to be summarized
      </p>
      <br>
    </md-card>
  </div>
</template>

<script>
const sha256 = require("js-sha256").sha256
export default {
  name: 'MerkleTree',
  data () {
    return {
      n: 0, // number of transactions
      txs: [
        //list of transactions
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
        "tx11",
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
      allBranches: {},
      branches: [
        //list of all the branches
        //b1: [
        //list of all the hashes obtained by concatenating two leaf nodes
        //],
        //b2: [
        //list of all the hashes obtained by concatenating two nodes from branch 1
        //]
        //.
        //.
        //.
        //bn: [
        //list of all obtained by concatenating two nodes from branch n-1
        //]
      ],
      branchesCopy: [
        //list of hashes obtained by concatenating two child nodes and hashing them with double-SHA algorithm
      ],
      branchCounter: 0, //value depends on the number of transactions
      merkleroot: '', //alphanumeric string
      message: '', //string 
    }
  },
  async created() {
    await this.hashTxs();
    await this.calcBranches();
  },
  methods: {
    async hashTxs() {
      //hash the transactions and display the leaf nodes
      if (this.txs.length == 0) {
        this.message = `There are no transactions to be summarized`
      } 
      else {
        this.message = `Transactions to be summarized: ${this.txs}`
        this.txsCopy = Array.from(this.txs)
        this.makeElementsEven(this.txsCopy) 
        for (const tx of this.txsCopy) {
          this.hashedtxs.push(sha256(sha256(tx)))
        }
      }
    },
    async calcBranches() {
      //calculate the branches and display the list of hashes until there is only one hash left 

      const rep = Math.ceil(Math.log2(this.txsCopy.length))

      for (let i = 0; i <= rep; i += 1) {
        if (this.hashedtxs.length == 1) {
          this.merkleRoot = this.hashedtxs[0]
          this.message = `The Merkle Root is ${this.merkleRoot}`
          console.log(this.message)
        } else if (this.branches.length == 1) {
          this.merkleRoot = this.branches[0]
          this.message = `The Merkle Root is ${this.merkleRoot}`
          console.log(this.message)
        } else if (this.hashedtxs.length > 1 && this.branches.length == 0) {
          this.hashedtxs.forEach((item, index) => {
            if (index % 2 == 0) {
              this.branches.push(
                sha256(sha256(this.hashedtxs[index].concat(this.hashedtxs[index + 1])))
              )
            }
          })
          this.message = `Branch: ${(this.branchCounter += 1)}`;
          console.log(this.message)
          console.log(this.branches)
          this.allBranches[this.branchCounter] = this.branches;
          if (this.branches.length > 1 && this.branches.length % 2 != 0) {
            this.makeElementsEven(this.branches)
            this.message = `Branch: ${(this.branchCounter += 1)}`;
            console.log(this.message)
            console.log(this.branches)
            this.allBranches[this.branchCounter] = this.branches;
          }
        } else if (this.branches.length > 0) {
          this.branchesCopy = Array.from(this.branches)
          this.branches = []
          this.branchesCopy.forEach((item, index) => {
            if (index % 2 == 0) {
              this.branches.push(
                sha256(sha256(this.branchesCopy[index].concat(this.branchesCopy[index + 1])))
              )
            }
          })
            this.message = `Branch: ${(this.branchCounter += 1)}`;
            console.log(this.message)  
          console.log(this.branches)
          this.allBranches[this.branchCounter] = this.branches;
          if (this.branches.length > 1 && this.branches.length % 2 != 0) {
            this.makeElementsEven(this.branches)
            this.message = `Branch: ${(this.branchCounter += 1)}`;
            console.log(this.message)  
            console.log(this.branches)
            this.allBranches[this.branchCounter] = this.branches;
          }
        }
      }
      this.message = `All the branches`;
      console.log(this.message)  
      console.log(this.allBranches)
    },
    makeElementsEven(arr) {
      if (arr.length > 1 && arr.length % 2 != 0) {
      arr.push(arr[arr.length - 1])
      }
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
#app li {
  display: inline;
  horizontal-align: center;
}
p{
  display: inline;
  }
</style>
