import React, {useEffect, useState} from 'react';
import logo from './logo.svg';
import './App.css';

import Web3 from "web3";
import Web3Modal from "web3modal";
import WalletConnectProvider from "@walletconnect/web3-provider";


function App() {
  const [web3Modal, setWeb3Modal] = useState(undefined);
  const [provider, setProvider] = useState(undefined);
  const [web3, setWeb3] = useState(undefined);
  // Update the document title using the browser API
  const providerOptions = {
    walletconnect: {
      package: WalletConnectProvider,
      options: {
        rpc: {
          97: 'https://data-seed-prebsc-1-s1.binance.org:8545/'
        },
        network: 'binance-testnet',
      }
    }
  };
  useEffect(() => {
    const init = async () => {
      try {
        if(!provider) {
          const web3Modal = await new Web3Modal({
            cacheProvider: false, // optional
            disableInjectedProvider: false,
            providerOptions // required
          });
          web3Modal.clearCachedProvider();
          await setWeb3Modal(web3Modal);          
        }
      } catch (err) {
        console.log(err.message);        
      }
    };
    init();
  }, []);

  const onConnect = async () => {
    console.log("connecting...");
    try {            
      const provider = await web3Modal.connect();
      // Get a Web3 instance for the wallet
      const web3 = await new Web3(provider);
      
      await setProvider(provider);
      await setWeb3(web3);
      // console.log("Web3 instance is", web3);
      // const chainId = await web3.eth.getChainId();
      // console.log(chainId);
      await fetchData()
    } catch ( err ){
      console.log(err.message);
    }
  }

  const fetchData = async () =>{    
    console.log("Web3 instance is", web3);
    // Get connected chain id from Ethereum node
    const chainId = await web3.eth.getChainId();
    console.log(chainId);

    // Get list of accounts of the connected wallet
    const accounts = await web3.eth.getAccounts();
    console.log(accounts)
  }

  return (
    <div>
      <button type="button" onClick={onConnect}>connect</button>
    </div>
  );
}

export default App;
