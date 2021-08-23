<template>
  <div class="hello">
    <h1>{{ msg }}</h1>
  </div>

  <div class="container">
    <!-- <div class="btn" v-on:click="Test1">Testing Button</div>
    <div class="btn" v-on:click="Approve">Approve Button</div>
    <div class="btn" v-on:click="Deposit">Deposit Button</div> -->

    <div class="btn" v-on:click="Approve('BNB')">Approve BNB</div>
    <div class="btn" v-on:click="Approve('BUSD')">Approve BUSD</div>
  </div>
</template>

<script lang="ts">
import { defineComponent } from "vue";
import Web3 from "web3";
import { ContractOptions } from "web3-eth-contract";
import { Contract } from "web3-eth-contract";
import { AbiItem } from "web3-utils";
import erc20 from "./erc20.json";
import masterChef from "./master-chef.json";

const TOKEN_ADDRESS       = "0x0000000000000000000000000000000000001010";
const MASTER_CHEF_ADDRESS = "0x7319eAbBBcc4fBBb778FD11c16A929c5DF62A547";
const WRAPPED_MATIC_TEST  = "0x0000000000000000000000000000000000001010";

export default defineComponent({
  components: {},
  data: function() {
    return {
      // qwe: "This is a test"
      // web3: null

      web3: new Web3(Web3.givenProvider || "https://bsc-dataseed1.ninicoin.io")
      // web3: new Web3("https://rpc-mumbai.maticvigil.com")
    };
  },
  created: async function() {
    this.web3 = new Web3(Web3.givenProvider || "https://bsc-dataseed1.ninicoin.io");
    // this.web3 = new Web3(Web3.givenProvider || "https://rpc-mumbai.maticvigil.com");

    try {
      // Ask user permission to access accounts
      await this.web3.eth.requestAccounts();
    } catch (error) {
      console.log(error);
    }

    const accAddr = await this.web3.eth.getAccounts();
    console.log("============================================================");
    console.log(accAddr);
    console.log(accAddr[0]);

    // if (window.ethereum) {
    //   window.web3 = new Web3(ethereum);
    //   try {
    //     // Request account access if needed
    //     ethereum.enable();
    //   } catch (error) {
    //     // User denied account access...
    //   }
    // } else if (window.web3) { // Legacy dapp browsers...
    //   window.web3 = new Web3(web3.currentProvider);
    // } else { // Non-dapp browsers...
    //   console.log('Non-Ethereum browser detected. You should consider trying MetaMask!');
    // }
  },
  methods: {
    Test1: async function() {
      // console.log(this);
      // console.log(this.qwe);
      console.log(await this.web3.eth.getAccounts());
    },
    Approve: async function(token: string) {
      let tokenAddress;

      if      (token === "BNB")  tokenAddress = "0xbb4CdB9CBd36B01bD1cBaEBF2De08d9173bc095c";
      else if (token === "BUSD") tokenAddress = "0xe9e7CEA3DedcA5984780Bafc599bD69ADd087D56";
      else return;

      const MAX_HEX = "0xffffffffffffffffffffffffffffffffffffffffffffffffffffffffffffffff";
      const MAX_DEC = "115792089237316195423570985008687907853269984665640564039457584007913129639935";
      // const contractOptions: ContractOptions = {};

      const contract = new this.web3.eth.Contract(erc20 as unknown as AbiItem, tokenAddress);
      const accAddr = (await this.web3.eth.getAccounts())[0];

      const tx = contract.methods
        .approve(MASTER_CHEF_ADDRESS, MAX_DEC)
        .send({ from: accAddr });
    },
    Deposit: async function() {
      const REFERRER_ADDRESS    = "0x0000000000000000000000000000000000000000";
      const ONE = "1000000000000000000";

      const contract = new this.web3.eth.Contract(masterChef as unknown as AbiItem, MASTER_CHEF_ADDRESS);
      const accAddr = (await this.web3.eth.getAccounts())[0];

      const tx = contract.methods
        .deposit(0, ONE, REFERRER_ADDRESS)
        .send({ from: accAddr });
    }
  }
});
</script>

<style scoped lang="scss">
.container {
  display: flex;
  justify-content: center;
  flex-direction: column;
  align-content: center;
  align-items: center;

  div {
    margin-bottom: 4px;
  }
}

.btn {
  background-color: rgb(20, 255, 255);
  border: 1px solid black;
  border-radius: 4px;
  width: 120px;
  padding: 4px 0;
  cursor: pointer;
  user-select: none;
  -ms-user-select: none;
  -moz-user-select: none;
  -khtml-user-select: none;
  -webkit-user-select: none;

  &:hover {
    background-color: rgb(110, 255, 255);
  }

  &:active {
    background-color: rgb(0, 228, 228);
    transform: translateY(1px);
  }
}

h3 {
  margin: 40px 0 0;
}

ul {
  list-style-type: none;
  padding: 0;
}

li {
  display: inline-block;
  margin: 0 10px;
}

a {
  color: #42b983;
}
</style>
