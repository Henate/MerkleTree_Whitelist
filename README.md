### 安装

#### 链下部分

```
npm install merkletreejs
```

```
npm install keccak256
```

#### 链上部分

```
npm install @openzeppelin/contracts
```



### 生成MerkleTree Root

在merkle_tree.js中完成以下行为

#### 录入白名单

```
let whitelistAddresses = [	// just example
    "0X5B38DA6A701C568545DCFCB03FCB875F56BEDDC4",
    "0X5A641E5FB72A2FD9137312E7694D42996D689D99",
    "0XDCAB482177A592E424D1C8318A464FC922E8DE40",
    "0X6E21D37E07A6F7E53C7ACE372CEC63D4AE4B6BD0",
    "0X09BAAB19FC77C19898140DADD30C4685C597620B",
    "0XCC4C29997177253376528C05D3DF91CF2D69061A",
    "0xdD870fA1b7C4700F2BD7f44238821C26f7392148" 
  ];
```

#### 生成root

```
const leafNodes = whitelistAddresses.map(addr => keccak256(addr));
const merkleTree = new MerkleTree(leafNodes, keccak256, { sortPairs: true});
const rootHash = merkleTree.getRoot();
```



### 智能合约校验

在MerkleTree.sol中完成以下行为

#### 构建智能合约时传入Root

```
constructor(bytes32 _root) ERC721("MerkleTreeWhiteList", "MTWL") {
root = _root;
}
```

#### Mint时使用校验方法

```

function isValid(bytes32[] memory proof, bytes32 leaf) public view returns (bool) {
	return MerkleProof.verify(proof, root, leaf);
}

function safeMint(address to, bytes32[] memory proof) public onlyOwner {
	require(isValid(proof, keccak256(abi.encodePacked(msg.sender))), "Not a part of Allowlist");
	uint256 tokenId = _tokenIdCounter.current();
	_tokenIdCounter.increment();
	_safeMint(to, tokenId);
}

```

