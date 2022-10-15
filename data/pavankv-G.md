gas Optimisation :-
 
https://github.com/code-423n4/2022-10-traderjoe/blob/main/test/TestHelper.sol

code snippet :-
   uint24 internal constant ID_ONE = 2**23;
    uint256 internal constant BASIS_POINT_MAX = 10_000;

    uint24 internal constant DEFAULT_MAX_VOLATILITY_ACCUMULATED = 1_777_638;
    uint16 internal constant DEFAULT_FILTER_PERIOD = 50;
    uint16 internal constant DEFAULT_DECAY_PERIOD = 100;
    uint16 internal constant DEFAULT_BIN_STEP = 25;
    uint16 internal constant DEFAULT_BASE_FACTOR = 5000;
    uint16 internal constant DEFAULT_PROTOCOL_SHARE = 1000;
    uint16 internal constant DEFAULT_SAMPLE_LIFETIME = 120;
    uint16 internal constant DEFAULT_REDUCTION_FACTOR = 5000;
    uint24 internal constant DEFAULT_VARIABLE_FEE_CONTROL = 5000;

    address payable internal constant DEV = payable(0xb4c79daB8f259C7Aee6E5b2Aa729821864227e84);
    address payable internal constant ALICE = payable(0xf39Fd6e51aad88F6F4ce6aB8827279cffFb92266);
    address payable internal constant BOB = payable(0x70997970C51812dc3A010C7d01b50e0d17dc79C8);

    address internal constant JOE_V1_FACTORY_ADDRESS = 0x9Ad6C38BE94206cA50bb0d90783181662f0Cfa10;
    address internal constant JOE_V1_ROUTER_ADDRESS = 0x60aE616a2155Ee3d9A68541Ba4544862310933d4;
    address internal constant WAVAX_AVALANCHE_ADDRESS = 0xB31f66AA3C1e785363F0875A1B74E27b85FD66c7;
    address internal constant USDC_AVALANCHE_ADDRESS = 0xB97EF9Ef8734C71904D8002F8b6Bc66Dd9c48a6E;


this all constant variables means you cache that in memory in constructor once delpoyed you can use till delpoyed contract end .and again you called above constant variable many times same contract files  you can more gas i hope this not required poc for . Try to change this to memory cache .

* And try to use uint256 because solidity first convert all (> uint256) to uint256 , so conversion use gas . 
---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------
2. Use pre-increment instead of post increment it will help to save gas :-

code snippet:-
https://github.com/code-423n4/2022-10-traderjoe/blob/main/test/TestHelper.sol
line :- 159


        for (uint256 i; i < _numberBins; i++) 

 change it to :-

        for (uint256 i; i < _numberBins; ++i)
