<template>
  <div id="app">
    <md-card class="txCss">
      <br> <br>
      <p class="title"> MerkleTree </p>
      <br><br>
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
      <p class="subtitle" v-if="txs.length==0">
        There are no transactions to be summarized
      </p> <br>
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
        /*"tx10",
        "tx11",*/
      ],
      final: [],
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
      xyzBranches:[
        //test branch
      ],
      allBranches: [
        //[
        //array containing all the corrected branches
        //],
	],
      tempBranches: [
	//array containing all the branches
	],
      branch: [
        //list of hashes obtained by concatenating two child nodes and hashing them with double-SHA algorithm
      ],
      branchCopy: [
        //copy of branches
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

<style scoped>
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
  }
</style>
