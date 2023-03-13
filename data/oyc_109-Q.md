
## [L-01] Unspecific Compiler Version Pragma

Avoid floating pragmas for non-library contracts.

While floating pragmas make sense for libraries to allow them to be included with multiple different versions of applications, it may be a security risk for application implementations.

A known vulnerable compiler version may accidentally be selected or security tools might fall-back to an older compiler version ending up checking a different EVM compilation that is ultimately deployed on the blockchain.

It is recommended to pin to a concrete compiler version.

```
2023-03-neotokyo/contracts/access/PermitControl.sol::2 => pragma solidity ^0.8.19;
2023-03-neotokyo/contracts/s1/BYTESContract.sol::5 => pragma solidity ^0.8.0;
2023-03-neotokyo/contracts/s1/NTItems.sol::10 => pragma solidity ^0.8.0;
2023-03-neotokyo/contracts/s1/NTLandDeploy.sol::10 => pragma solidity ^0.8.0;
2023-03-neotokyo/contracts/s1/beckLoot.sol::10 => pragma solidity ^0.8.0;
2023-03-neotokyo/contracts/s1/vaultBox.sol::10 => pragma solidity ^0.8.0;
2023-03-neotokyo/contracts/s2/NTOuterIdentity.sol::10 => pragma solidity ^0.8.0;
2023-03-neotokyo/contracts/s2/NTS2Items.sol::10 => pragma solidity ^0.8.0;
2023-03-neotokyo/contracts/s2/NTS2LandDeploy.sol::10 => pragma solidity ^0.8.0;
2023-03-neotokyo/contracts/staking/BYTES2.sol::2 => pragma solidity ^0.8.19;
2023-03-neotokyo/contracts/staking/NeoTokyoStaker.sol::2 => pragma solidity ^0.8.19;
```

## [L-02] Use of Block.timestamp

Block timestamps have historically been used for a variety of applications, such as entropy for random numbers (see the Entropy Illusion for further details), locking funds for periods of time, and various state-changing conditional statements that are time-dependent. Miners have the ability to adjust timestamps slightly, which can prove to be dangerous if block timestamps are used incorrectly in smart contracts.

```
2023-03-neotokyo/contracts/access/PermitControl.sol::195 => return permissions[_address][_circumstance][_right] > block.timestamp;
2023-03-neotokyo/contracts/s1/NTCitizenDeploy.sol::1379 => return _rewardRateByCitizenId[tokenId] + ((block.timestamp - _citizenCreationTime[tokenId]) / SECONDS_IN_A_YEAR);
2023-03-neotokyo/contracts/s1/NTCitizenDeploy.sol::1385 => if(block.timestamp < getEnd())
2023-03-neotokyo/contracts/s1/NTCitizenDeploy.sol::1387 => return block.timestamp;
2023-03-neotokyo/contracts/s1/NTCitizenDeploy.sol::1470 => _citizenCreationTime[newestCitizen] = block.timestamp;
2023-03-neotokyo/contracts/s1/NTCitizenDeploy.sol::1662 => creationTime = block.timestamp;
2023-03-neotokyo/contracts/s2/NTOuterCitizenDeploy.sol::1398 => _citizenCreationTime[newestCitizen] = block.timestamp;
2023-03-neotokyo/contracts/s2/NTOuterCitizenDeploy.sol::1562 => creationTime = block.timestamp;
2023-03-neotokyo/contracts/s2/NTOuterIdentity.sol::1287 => beginReserve = block.timestamp;
2023-03-neotokyo/contracts/s2/NTOuterIdentity.sol::1329 => beginBurn = block.timestamp;
2023-03-neotokyo/contracts/s2/NTOuterIdentity.sol::1462 => require(block.timestamp < (beginBurn + timeToBurn), "Burning has ended");
2023-03-neotokyo/contracts/s2/NTOuterIdentity.sol::1463 => return (beginBurn + timeToBurn) - block.timestamp;
2023-03-neotokyo/contracts/s2/NTOuterIdentity.sol::1469 => require(block.timestamp < (beginReserve + earlyReservePeriod), "Public minting is open");
2023-03-neotokyo/contracts/s2/NTOuterIdentity.sol::1470 => return (beginReserve + earlyReservePeriod) - block.timestamp;
2023-03-neotokyo/contracts/s2/NTOuterIdentity.sol::1475 => require(block.timestamp > (beginReserve + earlyReservePeriod) || validateCitizen(), "You cannot reserve yet");
2023-03-neotokyo/contracts/s2/NTOuterIdentity.sol::1500 => require(block.timestamp < (timeToBurn + beginBurn), "Burn auction has ended");
2023-03-neotokyo/contracts/staking/NeoTokyoStaker.sol::971 => citizenStatus.timelockEndTime = block.timestamp + timelockDuration;
2023-03-neotokyo/contracts/staking/NeoTokyoStaker.sol::1023 => citizenStatus.timelockEndTime = block.timestamp + timelockDuration;
2023-03-neotokyo/contracts/staking/NeoTokyoStaker.sol::1159 => block.timestamp + timelockDuration;
2023-03-neotokyo/contracts/staking/NeoTokyoStaker.sol::1215 => if (_pools[_assetType].rewardWindows[0].startTime >= block.timestamp) {
2023-03-neotokyo/contracts/staking/NeoTokyoStaker.sol::1329 => if (block.timestamp <= window.startTime) {
2023-03-neotokyo/contracts/staking/NeoTokyoStaker.sol::1331 => uint256 timeSinceReward = block.timestamp - lastPoolRewardTime;
2023-03-neotokyo/contracts/staking/NeoTokyoStaker.sol::1356 => block.timestamp - lastPoolRewardTime;
2023-03-neotokyo/contracts/staking/NeoTokyoStaker.sol::1378 => uint256 timeSinceReward = block.timestamp - lastPoolRewardTime;
2023-03-neotokyo/contracts/staking/NeoTokyoStaker.sol::1433 => lastRewardTime[_recipient][AssetType.S1_CITIZEN] = block.timestamp;
2023-03-neotokyo/contracts/staking/NeoTokyoStaker.sol::1434 => lastRewardTime[_recipient][AssetType.S2_CITIZEN] = block.timestamp;
2023-03-neotokyo/contracts/staking/NeoTokyoStaker.sol::1435 => lastRewardTime[_recipient][AssetType.LP] = block.timestamp;
2023-03-neotokyo/contracts/staking/NeoTokyoStaker.sol::1467 => if (block.timestamp < stakedCitizen.timelockEndTime) {
2023-03-neotokyo/contracts/staking/NeoTokyoStaker.sol::1542 => if (block.timestamp < stakedCitizen.timelockEndTime) {
2023-03-neotokyo/contracts/staking/NeoTokyoStaker.sol::1605 => if (block.timestamp < lpPosition.timelockEndTime) {
```

## [L-03] abi.encodePacked() should not be used with dynamic types when passing the result to a hash function such as keccak256()

Use abi.encode() instead which will pad items to 32 bytes, which will prevent hash collisions (e.g. abi.encodePacked(0x123,0x456) => 0x123456 => abi.encodePacked(0x1,0x23456), but abi.encode(0x123,0x456) => 0x0...1230...456). Unless there is a compelling reason, abi.encode should be preferred. If there is only one argument to abi.encodePacked() it can often be cast to bytes() or bytes32() instead.

```
2023-03-neotokyo/contracts/s1/NTItems.sol::1528 => uint256 rand = uint256(keccak256(abi.encodePacked(spotOnLeaderboard))) % 100;
2023-03-neotokyo/contracts/s1/NTItems.sol::1566 => bytes32 node = keccak256(abi.encodePacked(index, vaultTokenId, leaderboardSpot));
2023-03-neotokyo/contracts/s1/NTItems.sol::1570 => node = keccak256(abi.encodePacked(proof[i], node));
2023-03-neotokyo/contracts/s1/NTItems.sol::1572 => node = keccak256(abi.encodePacked(node, proof[i]));
2023-03-neotokyo/contracts/s1/NTLandDeploy.sol::1403 => bytes32 node = keccak256(abi.encodePacked(index, vaultTokenId, leaderboardSpot));
2023-03-neotokyo/contracts/s1/NTLandDeploy.sol::1407 => node = keccak256(abi.encodePacked(proof[i], node));
2023-03-neotokyo/contracts/s1/NTLandDeploy.sol::1409 => node = keccak256(abi.encodePacked(node, proof[i]));
2023-03-neotokyo/contracts/s1/beckLoot.sol::1525 => bytes32 node = keccak256(abi.encodePacked(index, wallet, amount));
2023-03-neotokyo/contracts/s1/beckLoot.sol::1529 => node = keccak256(abi.encodePacked(proof[i], node));
2023-03-neotokyo/contracts/s1/beckLoot.sol::1531 => node = keccak256(abi.encodePacked(node, proof[i]));
2023-03-neotokyo/contracts/s1/vaultBox.sol::1489 => bytes32 node = keccak256(abi.encodePacked(index, wallet, amount));
2023-03-neotokyo/contracts/s1/vaultBox.sol::1493 => node = keccak256(abi.encodePacked(proof[i], node));
2023-03-neotokyo/contracts/s1/vaultBox.sol::1495 => node = keccak256(abi.encodePacked(node, proof[i]));
2023-03-neotokyo/contracts/s2/NTOuterIdentity.sol::1535 => bytes32 node = keccak256(abi.encodePacked(index, wallet, spotInLeaderboard));
2023-03-neotokyo/contracts/s2/NTOuterIdentity.sol::1539 => node = keccak256(abi.encodePacked(proof[i], node));
2023-03-neotokyo/contracts/s2/NTOuterIdentity.sol::1541 => node = keccak256(abi.encodePacked(node, proof[i]));
2023-03-neotokyo/contracts/s2/NTS2Items.sol::1383 => bytes32 node = keccak256(abi.encodePacked(index, identityTokenId, leaderboardSpot));
2023-03-neotokyo/contracts/s2/NTS2Items.sol::1387 => node = keccak256(abi.encodePacked(proof[i], node));
2023-03-neotokyo/contracts/s2/NTS2Items.sol::1389 => node = keccak256(abi.encodePacked(node, proof[i]));
2023-03-neotokyo/contracts/s2/NTS2LandDeploy.sol::1356 => bytes32 node = keccak256(abi.encodePacked(index, identityTokenId, leaderboardSpot));
2023-03-neotokyo/contracts/s2/NTS2LandDeploy.sol::1360 => node = keccak256(abi.encodePacked(proof[i], node));
2023-03-neotokyo/contracts/s2/NTS2LandDeploy.sol::1362 => node = keccak256(abi.encodePacked(node, proof[i]));
```

## [L-04] require() should be used instead of assert()

require() should be used for checking error conditions on inputs and return values while assert() should be used for invariant checking

```
2023-03-neotokyo/contracts/s1/BYTESContract.sol::390 => // assert(a == b * c + a % b); // There is no case in which this doesn't hold
```

## [L-05] Unsafe use of transfer()/transferFrom() with IERC20

Some tokens do not implement the ERC20 standard properly but are still accepted by most code that accepts ERC20 tokens. For example Tether (USDT)'s transfer() and transferFrom() functions do not return booleans as the specification requires, and instead have no return value. When these sorts of tokens are cast to IERC20, their function signatures do not match and therefore the calls made, revert. Use OpenZeppelin’s SafeERC20's safeTransfer()/safeTransferFrom() instead

```
2023-03-neotokyo/contracts/s1/NTCitizenDeploy.sol::1440 => _identityContract.transferFrom(_msgSender(), address(this), identityId);
2023-03-neotokyo/contracts/s1/NTCitizenDeploy.sol::1445 => _vaultContract.transferFrom(_msgSender(), address(this), vaultId);
2023-03-neotokyo/contracts/s1/NTCitizenDeploy.sol::1451 => _itemContract.transferFrom(_msgSender(), address(this), itemCacheId);
2023-03-neotokyo/contracts/s1/NTCitizenDeploy.sol::1454 => _landContract.transferFrom(_msgSender(), address(this), landDeedId);
2023-03-neotokyo/contracts/s1/NTCitizenDeploy.sol::1489 => _identityContract.transferFrom(address(this), _msgSender(), _identityDataByCitizenId[citizenId]);
2023-03-neotokyo/contracts/s1/NTCitizenDeploy.sol::1494 => _vaultContract.transferFrom(address(this), _msgSender(), _vaultDataByCitizenId[citizenId]);
2023-03-neotokyo/contracts/s1/NTCitizenDeploy.sol::1498 => _itemContract.transferFrom(address(this), _msgSender(), _itemCacheDataByCitizenId[citizenId]);
2023-03-neotokyo/contracts/s1/NTCitizenDeploy.sol::1501 => _landContract.transferFrom(address(this), _msgSender(), _landDeedDataByCitizenId[citizenId]);
2023-03-neotokyo/contracts/s1/NTCitizenDeploy.sol::1630 => ERC721.transferFrom(from, to, tokenId);
2023-03-neotokyo/contracts/s2/NTOuterCitizenDeploy.sol::1377 => _identityContract.transferFrom(_msgSender(), address(this), identityId);
2023-03-neotokyo/contracts/s2/NTOuterCitizenDeploy.sol::1380 => _itemContract.transferFrom(_msgSender(), address(this), itemCacheId);
2023-03-neotokyo/contracts/s2/NTOuterCitizenDeploy.sol::1383 => _landContract.transferFrom(_msgSender(), address(this), landDeedId);
2023-03-neotokyo/contracts/s2/NTOuterCitizenDeploy.sol::1414 => _identityContract.transferFrom(address(this), _msgSender(), _identityDataByCitizenId[citizenId]);
2023-03-neotokyo/contracts/s2/NTOuterCitizenDeploy.sol::1417 => _itemContract.transferFrom(address(this), _msgSender(), _itemCacheDataByCitizenId[citizenId]);
2023-03-neotokyo/contracts/s2/NTOuterCitizenDeploy.sol::1420 => _landContract.transferFrom(address(this), _msgSender(), _landDeedDataByCitizenId[citizenId]);
```

## [L-06] Events not emitted for important state changes

When changing state variables events are not emitted. Emitting events allows monitoring activities with off-chain monitoring tools.

```
2023-03-neotokyo/contracts/s1/BYTESContract.sol::900 => function setIdentityContract(address _address) public onlyOwner {
2023-03-neotokyo/contracts/s1/BYTESContract.sol::904 => function setBoughtIdentityContract(address _address) public onlyOwner {
2023-03-neotokyo/contracts/s1/BYTESContract.sol::908 => function setVaultBoxContract(address _address) public onlyOwner {
2023-03-neotokyo/contracts/s1/BYTESContract.sol::912 => function setCitizenContract(address _address) public onlyOwner {
2023-03-neotokyo/contracts/s1/NTCitizenDeploy.sol::164 => function setApprovalForAll(address operator, bool _approved) external;
2023-03-neotokyo/contracts/s1/NTCitizenDeploy.sol::1531 => function setChangeGenderCost(uint256 _cost) external onlyOwner {
2023-03-neotokyo/contracts/s1/NTCitizenDeploy.sol::1535 => function setChangeMessageCost(uint256 _cost) external onlyOwner {
2023-03-neotokyo/contracts/s1/NTCitizenDeploy.sol::1567 => function setFemaleActive() public onlyOwner {
2023-03-neotokyo/contracts/s1/NTCitizenDeploy.sol::1571 => function setCitizenMintActive() public onlyOwner {
2023-03-neotokyo/contracts/s1/NTCitizenDeploy.sol::1575 => function setBoughtIdentitiesActive() public onlyOwner {
2023-03-neotokyo/contracts/s1/NTCitizenDeploy.sol::1579 => function setIdentityAddress(address contractAddress) public onlyOwner {
2023-03-neotokyo/contracts/s1/NTCitizenDeploy.sol::1583 => function setLandContract(address contractAddress) public onlyOwner {
2023-03-neotokyo/contracts/s1/NTCitizenDeploy.sol::1587 => function setItemContract(address contractAddress) public onlyOwner {
2023-03-neotokyo/contracts/s1/NTCitizenDeploy.sol::1591 => function setVaultAddress(address contractAddress) public onlyOwner {
2023-03-neotokyo/contracts/s1/NTCitizenDeploy.sol::1595 => function setBytesAddress(address contractAddress) public onlyOwner {
2023-03-neotokyo/contracts/s1/NTCitizenDeploy.sol::1599 => function setCitizenMintContract(address contractAddress) public onlyOwner {
2023-03-neotokyo/contracts/s1/NTCitizenDeploy.sol::1603 => function setCitizenAlternateMintContract(address contractAddress) public onlyOwner {
2023-03-neotokyo/contracts/s1/NTCitizenDeploy.sol::1607 => function setMintedIdentityCost(uint256 _cost) public onlyOwner {
2023-03-neotokyo/contracts/s1/NTItems.sol::144 => function setApprovalForAll(address operator, bool _approved) external;
2023-03-neotokyo/contracts/s1/NTItems.sol::1432 => function setItemMint() external onlyOwner
2023-03-neotokyo/contracts/s1/NTItems.sol::1589 => function setItemMintActive() public onlyOwner {
2023-03-neotokyo/contracts/s1/NTItems.sol::1593 => function setItemCost(uint256 _cost) public onlyOwner {
2023-03-neotokyo/contracts/s1/NTItems.sol::1597 => function setContract(address contractAddress) public onlyOwner {
2023-03-neotokyo/contracts/s1/NTItems.sol::1601 => function setBoxAddress(address contractAddress) public onlyOwner {
2023-03-neotokyo/contracts/s1/NTItems.sol::1605 => function setBytesAddress(address contractAddress) public onlyOwner {
2023-03-neotokyo/contracts/s1/NTLandDeploy.sol::144 => function setApprovalForAll(address operator, bool _approved) external;
2023-03-neotokyo/contracts/s1/NTLandDeploy.sol::1426 => function setLandMintActive() public onlyOwner {
2023-03-neotokyo/contracts/s1/NTLandDeploy.sol::1430 => function setLandCost(uint256 _cost) public onlyOwner {
2023-03-neotokyo/contracts/s1/NTLandDeploy.sol::1434 => function setContract(address contractAddress) public onlyOwner {
2023-03-neotokyo/contracts/s1/NTLandDeploy.sol::1438 => function setItemContract(address contractAddress) public onlyOwner {
2023-03-neotokyo/contracts/s1/NTLandDeploy.sol::1442 => function setVaultAddress(address contractAddress) public onlyOwner {
2023-03-neotokyo/contracts/s1/NTLandDeploy.sol::1446 => function setBytesAddress(address contractAddress) public onlyOwner {
2023-03-neotokyo/contracts/s1/beckLoot.sol::141 => function setApprovalForAll(address operator, bool _approved) external;
2023-03-neotokyo/contracts/s1/beckLoot.sol::1356 => function setSale() external onlyOwner
2023-03-neotokyo/contracts/s1/beckLoot.sol::1361 => function setWhitelistState() external onlyOwner
2023-03-neotokyo/contracts/s1/beckLoot.sol::1548 => function setMintContract(address contractAddress) public onlyOwner {
2023-03-neotokyo/contracts/s1/beckLoot.sol::1552 => function setRareMintContract(address contractAddress) public onlyOwner {
2023-03-neotokyo/contracts/s1/beckLoot.sol::1556 => function setHandMintContract(address contractAddress) public onlyOwner {
2023-03-neotokyo/contracts/s1/vaultBox.sol::141 => function setApprovalForAll(address operator, bool _approved) external;
2023-03-neotokyo/contracts/s1/vaultBox.sol::1338 => function setSale() external onlyOwner
2023-03-neotokyo/contracts/s1/vaultBox.sol::1343 => function setWhitelistState() external onlyOwner
2023-03-neotokyo/contracts/s1/vaultBox.sol::1348 => function setOpenClaimState() external onlyOwner
2023-03-neotokyo/contracts/s1/vaultBox.sol::1512 => function setContract(address contractAddress) public onlyOwner {
2023-03-neotokyo/contracts/s1/vaultBox.sol::1516 => function setIdAddress(address contractAddress) public onlyOwner {
2023-03-neotokyo/contracts/s2/NTOuterCitizenDeploy.sol::160 => function setApprovalForAll(address operator, bool _approved) external;
2023-03-neotokyo/contracts/s2/NTOuterCitizenDeploy.sol::1448 => function setChangeGenderCost(uint256 _cost) external onlyOwner {
2023-03-neotokyo/contracts/s2/NTOuterCitizenDeploy.sol::1452 => function setChangeMessageCost(uint256 _cost) external onlyOwner {
2023-03-neotokyo/contracts/s2/NTOuterCitizenDeploy.sol::1479 => function setFemaleActive() public onlyOwner {
2023-03-neotokyo/contracts/s2/NTOuterCitizenDeploy.sol::1483 => function setCitizenMintActive() public onlyOwner {
2023-03-neotokyo/contracts/s2/NTOuterCitizenDeploy.sol::1487 => function setBoughtIdentitiesActive() public onlyOwner {
2023-03-neotokyo/contracts/s2/NTOuterCitizenDeploy.sol::1491 => function setBoughtIdentityContract(address _address) external onlyOwner {
2023-03-neotokyo/contracts/s2/NTOuterCitizenDeploy.sol::1495 => function setIdentityAddress(address contractAddress) public onlyOwner {
2023-03-neotokyo/contracts/s2/NTOuterCitizenDeploy.sol::1499 => function setLandContract(address contractAddress) public onlyOwner {
2023-03-neotokyo/contracts/s2/NTOuterCitizenDeploy.sol::1503 => function setItemContract(address contractAddress) public onlyOwner {
2023-03-neotokyo/contracts/s2/NTOuterCitizenDeploy.sol::1507 => function setBytesAddress(address contractAddress) public onlyOwner {
2023-03-neotokyo/contracts/s2/NTOuterCitizenDeploy.sol::1511 => function setCitizenMintContract(address contractAddress) public onlyOwner {
2023-03-neotokyo/contracts/s2/NTOuterCitizenDeploy.sol::1515 => function setCitizenAlternateMintContract(address contractAddress) public onlyOwner {
2023-03-neotokyo/contracts/s2/NTOuterCitizenDeploy.sol::1519 => function setMintedIdentityCost(uint256 _cost) public onlyOwner {
2023-03-neotokyo/contracts/s2/NTOuterIdentity.sol::145 => function setApprovalForAll(address operator, bool _approved) external;
2023-03-neotokyo/contracts/s2/NTOuterIdentity.sol::1283 => function setSaleActive() external onlyOwner
2023-03-neotokyo/contracts/s2/NTOuterIdentity.sol::1290 => function setWhitelistState() external onlyOwner
2023-03-neotokyo/contracts/s2/NTOuterIdentity.sol::1295 => function setBurnState() external onlyOwner
2023-03-neotokyo/contracts/s2/NTOuterIdentity.sol::1305 => function setMaxMints(uint256 _value) external onlyOwner
2023-03-neotokyo/contracts/s2/NTOuterIdentity.sol::1310 => function setCitizenContract(address _address) external onlyOwner
2023-03-neotokyo/contracts/s2/NTOuterIdentity.sol::1315 => function setIdentityContract(address _address) external onlyOwner
2023-03-neotokyo/contracts/s2/NTOuterIdentity.sol::1558 => function setOuterIdentityMintContract(address contractAddress) public onlyOwner {
2023-03-neotokyo/contracts/s2/NTOuterIdentity.sol::1562 => function setWithdrawalAddress(address _address) external onlyOwner
2023-03-neotokyo/contracts/s2/NTS2Items.sol::144 => function setApprovalForAll(address operator, bool _approved) external;
2023-03-neotokyo/contracts/s2/NTS2Items.sol::1269 => function setItemMint() external onlyOwner
2023-03-neotokyo/contracts/s2/NTS2Items.sol::1406 => function setItemMintActive() public onlyOwner {
2023-03-neotokyo/contracts/s2/NTS2Items.sol::1410 => function setItemCost(uint256 _cost) public onlyOwner {
2023-03-neotokyo/contracts/s2/NTS2Items.sol::1414 => function setContract(address contractAddress) public onlyOwner {
2023-03-neotokyo/contracts/s2/NTS2Items.sol::1418 => function setIdentityAddress(address contractAddress) public onlyOwner {
2023-03-neotokyo/contracts/s2/NTS2Items.sol::1422 => function setBytesAddress(address contractAddress) public onlyOwner {
2023-03-neotokyo/contracts/s2/NTS2LandDeploy.sol::144 => function setApprovalForAll(address operator, bool _approved) external;
2023-03-neotokyo/contracts/s2/NTS2LandDeploy.sol::1379 => function setLandMintActive() public onlyOwner {
2023-03-neotokyo/contracts/s2/NTS2LandDeploy.sol::1383 => function setLandCost(uint256 _cost) public onlyOwner {
2023-03-neotokyo/contracts/s2/NTS2LandDeploy.sol::1387 => function setContract(address contractAddress) public onlyOwner {
2023-03-neotokyo/contracts/s2/NTS2LandDeploy.sol::1391 => function setIdentityContract(address contractAddress) public onlyOwner {
2023-03-neotokyo/contracts/s2/NTS2LandDeploy.sol::1395 => function setBytesAddress(address contractAddress) public onlyOwner {
```

## [L-07] _safeMint() should be used rather than _mint() wherever possible

Some tokens do not implement the ERC20 standard properly but are still accepted by most code that accepts ERC20 tokens. For example Tether (USDT)'s transfer() and transferFrom() functions do not return booleans as the specification requires, and instead have no return value. When these sorts of tokens are cast to IERC20, their function signatures do not match and therefore the calls made, revert. Use OpenZeppelin’s SafeERC20's safeTransfer()/safeTransferFrom() instead

```
2023-03-neotokyo/contracts/s1/BYTESContract.sol::716 => function _mint(address account, uint256 amount) internal virtual {
2023-03-neotokyo/contracts/s1/BYTESContract.sol::838 => _mint(msg.sender, payout);
2023-03-neotokyo/contracts/s1/BYTESContract.sol::981 => _mint(_to, reward * 10 ** 18);
2023-03-neotokyo/contracts/s1/NTCitizenDeploy.sol::940 => _mint(to, tokenId);
2023-03-neotokyo/contracts/s1/NTCitizenDeploy.sol::959 => function _mint(address to, uint256 tokenId) internal virtual {
2023-03-neotokyo/contracts/s1/NTItems.sol::920 => _mint(to, tokenId);
2023-03-neotokyo/contracts/s1/NTItems.sol::939 => function _mint(address to, uint256 tokenId) internal virtual {
2023-03-neotokyo/contracts/s1/NTLandDeploy.sol::920 => _mint(to, tokenId);
2023-03-neotokyo/contracts/s1/NTLandDeploy.sol::939 => function _mint(address to, uint256 tokenId) internal virtual {
2023-03-neotokyo/contracts/s1/beckLoot.sol::917 => _mint(to, tokenId);
2023-03-neotokyo/contracts/s1/beckLoot.sol::936 => function _mint(address to, uint256 tokenId) internal virtual {
2023-03-neotokyo/contracts/s1/vaultBox.sol::917 => _mint(to, tokenId);
2023-03-neotokyo/contracts/s1/vaultBox.sol::936 => function _mint(address to, uint256 tokenId) internal virtual {
2023-03-neotokyo/contracts/s2/NTOuterCitizenDeploy.sol::936 => _mint(to, tokenId);
2023-03-neotokyo/contracts/s2/NTOuterCitizenDeploy.sol::955 => function _mint(address to, uint256 tokenId) internal virtual {
2023-03-neotokyo/contracts/s2/NTOuterIdentity.sol::921 => _mint(to, tokenId);
2023-03-neotokyo/contracts/s2/NTOuterIdentity.sol::940 => function _mint(address to, uint256 tokenId) internal virtual {
2023-03-neotokyo/contracts/s2/NTS2Items.sol::920 => _mint(to, tokenId);
2023-03-neotokyo/contracts/s2/NTS2Items.sol::939 => function _mint(address to, uint256 tokenId) internal virtual {
2023-03-neotokyo/contracts/s2/NTS2LandDeploy.sol::920 => _mint(to, tokenId);
2023-03-neotokyo/contracts/s2/NTS2LandDeploy.sol::939 => function _mint(address to, uint256 tokenId) internal virtual {
2023-03-neotokyo/contracts/staking/BYTES2.sol::102 => _mint(msg.sender, _amount);
2023-03-neotokyo/contracts/staking/BYTES2.sol::124 => _mint(_to, reward);
2023-03-neotokyo/contracts/staking/BYTES2.sol::127 => _mint(TREASURY, daoCommision);
2023-03-neotokyo/contracts/staking/BYTES2.sol::154 => _mint(TREASURY, treasuryShare);
```

## [N-01] Use a more recent version of solidity

Use a solidity version of at least 0.8.4 to get bytes.concat() instead of abi.encodePacked(<bytes>,<bytes>)
Use a solidity version of at least 0.8.12 to get string.concat() instead of abi.encodePacked(<str>,<str>)
Use a solidity version of at least 0.8.13 to get the ability to use using for with a list of free functions

```
2023-03-neotokyo/contracts/s1/BYTESContract.sol::5 => pragma solidity ^0.8.0;
2023-03-neotokyo/contracts/s1/NTCitizenDeploy.sol::10 => pragma solidity 0.8.11;
2023-03-neotokyo/contracts/s1/NTItems.sol::10 => pragma solidity ^0.8.0;
2023-03-neotokyo/contracts/s1/NTLandDeploy.sol::10 => pragma solidity ^0.8.0;
2023-03-neotokyo/contracts/s1/beckLoot.sol::10 => pragma solidity ^0.8.0;
2023-03-neotokyo/contracts/s1/vaultBox.sol::10 => pragma solidity ^0.8.0;
2023-03-neotokyo/contracts/s2/NTOuterCitizenDeploy.sol::10 => pragma solidity 0.8.11;
2023-03-neotokyo/contracts/s2/NTOuterIdentity.sol::10 => pragma solidity ^0.8.0;
2023-03-neotokyo/contracts/s2/NTS2Items.sol::10 => pragma solidity ^0.8.0;
2023-03-neotokyo/contracts/s2/NTS2LandDeploy.sol::10 => pragma solidity ^0.8.0;
```

## [N-02] Use scientific notation (e.g. 1e18) rather than exponentiation (e.g. 10**18)

Scientific notation should be used for better code readability

```
2023-03-neotokyo/contracts/s1/BYTESContract.sol::834 => uint payout = credits * 10 ** 18;
2023-03-neotokyo/contracts/s1/BYTESContract.sol::981 => _mint(_to, reward * 10 ** 18);
2023-03-neotokyo/contracts/s2/NTOuterIdentity.sol::1502 => uint256 burnAmount = amount * 10 ** 18;
```

## [N-03] The visibility for constructor is ignored

No need to add public to constructor

```
2023-03-neotokyo/contracts/s1/BYTESContract.sol::544 => constructor (string memory name, string memory symbol) public {
```

## [N-04] Missing SPDX license identifier

File should include SPDX-License-Identifier

```
2023-03-neotokyo/contracts/s1/BYTESContract.sol::1 => /**
2023-03-neotokyo/contracts/s1/NTCitizenDeploy.sol::1 => /**
2023-03-neotokyo/contracts/s1/NTItems.sol::1 => /**
2023-03-neotokyo/contracts/s1/NTLandDeploy.sol::1 => /**
2023-03-neotokyo/contracts/s1/beckLoot.sol::1 => /**
2023-03-neotokyo/contracts/s1/vaultBox.sol::1 => /**
2023-03-neotokyo/contracts/s2/NTOuterCitizenDeploy.sol::1 => /**
2023-03-neotokyo/contracts/s2/NTOuterIdentity.sol::1 => /**
2023-03-neotokyo/contracts/s2/NTS2Items.sol::1 => /**
2023-03-neotokyo/contracts/s2/NTS2LandDeploy.sol::1 => /**
```

## [N-05] Missing event for critical parameter change

Emitting events after sensitive changes take place, to facilitate tracking and notify off-chain clients following changes to the contract.

```
2023-03-neotokyo/contracts/s1/BYTESContract.sol::900 => function setIdentityContract(address _address) public onlyOwner {
2023-03-neotokyo/contracts/s1/BYTESContract.sol::904 => function setBoughtIdentityContract(address _address) public onlyOwner {
2023-03-neotokyo/contracts/s1/BYTESContract.sol::908 => function setVaultBoxContract(address _address) public onlyOwner {
2023-03-neotokyo/contracts/s1/BYTESContract.sol::912 => function setCitizenContract(address _address) public onlyOwner {
2023-03-neotokyo/contracts/s1/NTCitizenDeploy.sol::164 => function setApprovalForAll(address operator, bool _approved) external;
2023-03-neotokyo/contracts/s1/NTCitizenDeploy.sol::1531 => function setChangeGenderCost(uint256 _cost) external onlyOwner {
2023-03-neotokyo/contracts/s1/NTCitizenDeploy.sol::1535 => function setChangeMessageCost(uint256 _cost) external onlyOwner {
2023-03-neotokyo/contracts/s1/NTCitizenDeploy.sol::1567 => function setFemaleActive() public onlyOwner {
2023-03-neotokyo/contracts/s1/NTCitizenDeploy.sol::1571 => function setCitizenMintActive() public onlyOwner {
2023-03-neotokyo/contracts/s1/NTCitizenDeploy.sol::1575 => function setBoughtIdentitiesActive() public onlyOwner {
2023-03-neotokyo/contracts/s1/NTCitizenDeploy.sol::1579 => function setIdentityAddress(address contractAddress) public onlyOwner {
2023-03-neotokyo/contracts/s1/NTCitizenDeploy.sol::1583 => function setLandContract(address contractAddress) public onlyOwner {
2023-03-neotokyo/contracts/s1/NTCitizenDeploy.sol::1587 => function setItemContract(address contractAddress) public onlyOwner {
2023-03-neotokyo/contracts/s1/NTCitizenDeploy.sol::1591 => function setVaultAddress(address contractAddress) public onlyOwner {
2023-03-neotokyo/contracts/s1/NTCitizenDeploy.sol::1595 => function setBytesAddress(address contractAddress) public onlyOwner {
2023-03-neotokyo/contracts/s1/NTCitizenDeploy.sol::1599 => function setCitizenMintContract(address contractAddress) public onlyOwner {
2023-03-neotokyo/contracts/s1/NTCitizenDeploy.sol::1603 => function setCitizenAlternateMintContract(address contractAddress) public onlyOwner {
2023-03-neotokyo/contracts/s1/NTCitizenDeploy.sol::1607 => function setMintedIdentityCost(uint256 _cost) public onlyOwner {
2023-03-neotokyo/contracts/s1/NTItems.sol::144 => function setApprovalForAll(address operator, bool _approved) external;
2023-03-neotokyo/contracts/s1/NTItems.sol::1432 => function setItemMint() external onlyOwner
2023-03-neotokyo/contracts/s1/NTItems.sol::1589 => function setItemMintActive() public onlyOwner {
2023-03-neotokyo/contracts/s1/NTItems.sol::1593 => function setItemCost(uint256 _cost) public onlyOwner {
2023-03-neotokyo/contracts/s1/NTItems.sol::1597 => function setContract(address contractAddress) public onlyOwner {
2023-03-neotokyo/contracts/s1/NTItems.sol::1601 => function setBoxAddress(address contractAddress) public onlyOwner {
2023-03-neotokyo/contracts/s1/NTItems.sol::1605 => function setBytesAddress(address contractAddress) public onlyOwner {
2023-03-neotokyo/contracts/s1/NTLandDeploy.sol::144 => function setApprovalForAll(address operator, bool _approved) external;
2023-03-neotokyo/contracts/s1/NTLandDeploy.sol::1426 => function setLandMintActive() public onlyOwner {
2023-03-neotokyo/contracts/s1/NTLandDeploy.sol::1430 => function setLandCost(uint256 _cost) public onlyOwner {
2023-03-neotokyo/contracts/s1/NTLandDeploy.sol::1434 => function setContract(address contractAddress) public onlyOwner {
2023-03-neotokyo/contracts/s1/NTLandDeploy.sol::1438 => function setItemContract(address contractAddress) public onlyOwner {
2023-03-neotokyo/contracts/s1/NTLandDeploy.sol::1442 => function setVaultAddress(address contractAddress) public onlyOwner {
2023-03-neotokyo/contracts/s1/NTLandDeploy.sol::1446 => function setBytesAddress(address contractAddress) public onlyOwner {
2023-03-neotokyo/contracts/s1/beckLoot.sol::141 => function setApprovalForAll(address operator, bool _approved) external;
2023-03-neotokyo/contracts/s1/beckLoot.sol::1356 => function setSale() external onlyOwner
2023-03-neotokyo/contracts/s1/beckLoot.sol::1361 => function setWhitelistState() external onlyOwner
2023-03-neotokyo/contracts/s1/beckLoot.sol::1548 => function setMintContract(address contractAddress) public onlyOwner {
2023-03-neotokyo/contracts/s1/beckLoot.sol::1552 => function setRareMintContract(address contractAddress) public onlyOwner {
2023-03-neotokyo/contracts/s1/beckLoot.sol::1556 => function setHandMintContract(address contractAddress) public onlyOwner {
2023-03-neotokyo/contracts/s1/vaultBox.sol::141 => function setApprovalForAll(address operator, bool _approved) external;
2023-03-neotokyo/contracts/s1/vaultBox.sol::1338 => function setSale() external onlyOwner
2023-03-neotokyo/contracts/s1/vaultBox.sol::1343 => function setWhitelistState() external onlyOwner
2023-03-neotokyo/contracts/s1/vaultBox.sol::1348 => function setOpenClaimState() external onlyOwner
2023-03-neotokyo/contracts/s1/vaultBox.sol::1512 => function setContract(address contractAddress) public onlyOwner {
2023-03-neotokyo/contracts/s1/vaultBox.sol::1516 => function setIdAddress(address contractAddress) public onlyOwner {
2023-03-neotokyo/contracts/s2/NTOuterCitizenDeploy.sol::160 => function setApprovalForAll(address operator, bool _approved) external;
2023-03-neotokyo/contracts/s2/NTOuterCitizenDeploy.sol::1448 => function setChangeGenderCost(uint256 _cost) external onlyOwner {
2023-03-neotokyo/contracts/s2/NTOuterCitizenDeploy.sol::1452 => function setChangeMessageCost(uint256 _cost) external onlyOwner {
2023-03-neotokyo/contracts/s2/NTOuterCitizenDeploy.sol::1479 => function setFemaleActive() public onlyOwner {
2023-03-neotokyo/contracts/s2/NTOuterCitizenDeploy.sol::1483 => function setCitizenMintActive() public onlyOwner {
2023-03-neotokyo/contracts/s2/NTOuterCitizenDeploy.sol::1487 => function setBoughtIdentitiesActive() public onlyOwner {
2023-03-neotokyo/contracts/s2/NTOuterCitizenDeploy.sol::1491 => function setBoughtIdentityContract(address _address) external onlyOwner {
2023-03-neotokyo/contracts/s2/NTOuterCitizenDeploy.sol::1495 => function setIdentityAddress(address contractAddress) public onlyOwner {
2023-03-neotokyo/contracts/s2/NTOuterCitizenDeploy.sol::1499 => function setLandContract(address contractAddress) public onlyOwner {
2023-03-neotokyo/contracts/s2/NTOuterCitizenDeploy.sol::1503 => function setItemContract(address contractAddress) public onlyOwner {
2023-03-neotokyo/contracts/s2/NTOuterCitizenDeploy.sol::1507 => function setBytesAddress(address contractAddress) public onlyOwner {
2023-03-neotokyo/contracts/s2/NTOuterCitizenDeploy.sol::1511 => function setCitizenMintContract(address contractAddress) public onlyOwner {
2023-03-neotokyo/contracts/s2/NTOuterCitizenDeploy.sol::1515 => function setCitizenAlternateMintContract(address contractAddress) public onlyOwner {
2023-03-neotokyo/contracts/s2/NTOuterCitizenDeploy.sol::1519 => function setMintedIdentityCost(uint256 _cost) public onlyOwner {
2023-03-neotokyo/contracts/s2/NTOuterIdentity.sol::145 => function setApprovalForAll(address operator, bool _approved) external;
2023-03-neotokyo/contracts/s2/NTOuterIdentity.sol::1283 => function setSaleActive() external onlyOwner
2023-03-neotokyo/contracts/s2/NTOuterIdentity.sol::1290 => function setWhitelistState() external onlyOwner
2023-03-neotokyo/contracts/s2/NTOuterIdentity.sol::1295 => function setBurnState() external onlyOwner
2023-03-neotokyo/contracts/s2/NTOuterIdentity.sol::1305 => function setMaxMints(uint256 _value) external onlyOwner
2023-03-neotokyo/contracts/s2/NTOuterIdentity.sol::1310 => function setCitizenContract(address _address) external onlyOwner
2023-03-neotokyo/contracts/s2/NTOuterIdentity.sol::1315 => function setIdentityContract(address _address) external onlyOwner
2023-03-neotokyo/contracts/s2/NTOuterIdentity.sol::1558 => function setOuterIdentityMintContract(address contractAddress) public onlyOwner {
2023-03-neotokyo/contracts/s2/NTOuterIdentity.sol::1562 => function setWithdrawalAddress(address _address) external onlyOwner
2023-03-neotokyo/contracts/s2/NTS2Items.sol::144 => function setApprovalForAll(address operator, bool _approved) external;
2023-03-neotokyo/contracts/s2/NTS2Items.sol::1269 => function setItemMint() external onlyOwner
2023-03-neotokyo/contracts/s2/NTS2Items.sol::1406 => function setItemMintActive() public onlyOwner {
2023-03-neotokyo/contracts/s2/NTS2Items.sol::1410 => function setItemCost(uint256 _cost) public onlyOwner {
2023-03-neotokyo/contracts/s2/NTS2Items.sol::1414 => function setContract(address contractAddress) public onlyOwner {
2023-03-neotokyo/contracts/s2/NTS2Items.sol::1418 => function setIdentityAddress(address contractAddress) public onlyOwner {
2023-03-neotokyo/contracts/s2/NTS2Items.sol::1422 => function setBytesAddress(address contractAddress) public onlyOwner {
2023-03-neotokyo/contracts/s2/NTS2LandDeploy.sol::144 => function setApprovalForAll(address operator, bool _approved) external;
2023-03-neotokyo/contracts/s2/NTS2LandDeploy.sol::1379 => function setLandMintActive() public onlyOwner {
2023-03-neotokyo/contracts/s2/NTS2LandDeploy.sol::1383 => function setLandCost(uint256 _cost) public onlyOwner {
2023-03-neotokyo/contracts/s2/NTS2LandDeploy.sol::1387 => function setContract(address contractAddress) public onlyOwner {
2023-03-neotokyo/contracts/s2/NTS2LandDeploy.sol::1391 => function setIdentityContract(address contractAddress) public onlyOwner {
2023-03-neotokyo/contracts/s2/NTS2LandDeploy.sol::1395 => function setBytesAddress(address contractAddress) public onlyOwner {
```

## [N-06] Expressions for constant values such as a call to keccak256(), should use immutable rather than constant

instances:

```
2023-03-neotokyo/contracts/staking/BYTES2.sol::37 => bytes32 public constant BURN = keccak256("BURN");
2023-03-neotokyo/contracts/staking/BYTES2.sol::40 => bytes32 public constant ADMIN = keccak256("ADMIN");
2023-03-neotokyo/contracts/staking/NeoTokyoStaker.sol::206 => bytes32 public constant CONFIGURE_LP = keccak256("CONFIGURE_LP");
2023-03-neotokyo/contracts/staking/NeoTokyoStaker.sol::209 => bytes32 public constant CONFIGURE_TIMELOCKS = keccak256(
2023-03-neotokyo/contracts/staking/NeoTokyoStaker.sol::214 => bytes32 public constant CONFIGURE_CREDITS = keccak256("CONFIGURE_CREDITS");
2023-03-neotokyo/contracts/staking/NeoTokyoStaker.sol::217 => bytes32 public constant CONFIGURE_POOLS = keccak256("CONFIGURE_POOLS");
2023-03-neotokyo/contracts/staking/NeoTokyoStaker.sol::220 => bytes32 public constant CONFIGURE_CAPS = keccak256("CONFIGURE_CAPS");
```

## [N-07] Lines are too long

Usually lines in source code are limited to 80 characters. Today's screens are much larger so it's reasonable to stretch this in some cases. Since the files will most likely reside in GitHub, and GitHub starts using a scroll bar in all cases when the length is over 164 characters, the lines below should be split when they reach that length

```
2023-03-neotokyo/contracts/s1/NTCitizenDeploy.sol::1410 => function createCitizen(uint256 identityId, uint256 vaultId, uint256 itemCacheId, uint256 landDeedId, bool genderFemale, string memory specialMessage) public nonReentrant {
2023-03-neotokyo/contracts/s1/NTLandDeploy.sol::1338 => function landClaim(uint256 vaultTokenId, uint256 identityTokenId, uint256 itemCacheTokenId, uint256 spotOnLeaderboard, uint256 spotInWhitelist, bytes32[] memory proof) public nonReentrant {
2023-03-neotokyo/contracts/s1/beckLoot.sol::1252 => uint BASE_CREDIT_VALUE = 250; // Multiply base credit value by probability of each classes spawn rate and their multiplier then add all of those values to get ~900k. We then 25 from this value and rand % 49 to get a range
```

