## [G-01] Don't Initialize Variables with Default Value

Uninitialized variables are assigned with the types default value. Explicitly initializing a variable with it's default value costs unnecesary gas.

```
2023-03-neotokyo/contracts/s1/BYTESContract.sol::1000 => for (uint256 i = 0; i < bytes(_str).length; i++) {
2023-03-neotokyo/contracts/s1/NTCitizenDeploy.sol::236 => uint256 length = 0;
2023-03-neotokyo/contracts/s1/NTCitizenDeploy.sol::1363 => for(uint256 index = 0; index < ERC721.balanceOf(_user); index++)
2023-03-neotokyo/contracts/s1/NTItems.sol::216 => uint256 length = 0;
2023-03-neotokyo/contracts/s1/NTItems.sol::1568 => for (uint16 i = 0; i < proof.length; i++) {
2023-03-neotokyo/contracts/s1/NTItems.sol::1578 => for(uint i = 0; i < _rootHash.length; i++)
2023-03-neotokyo/contracts/s1/NTLandDeploy.sol::216 => uint256 length = 0;
2023-03-neotokyo/contracts/s1/NTLandDeploy.sol::1405 => for (uint16 i = 0; i < proof.length; i++) {
2023-03-neotokyo/contracts/s1/NTLandDeploy.sol::1415 => for(uint i = 0; i < _rootHash.length; i++)
2023-03-neotokyo/contracts/s1/beckLoot.sol::213 => uint256 length = 0;
2023-03-neotokyo/contracts/s1/beckLoot.sol::1527 => for (uint16 i = 0; i < proof.length; i++) {
2023-03-neotokyo/contracts/s1/beckLoot.sol::1537 => for(uint i = 0; i < _rootHash.length; i++)
2023-03-neotokyo/contracts/s1/vaultBox.sol::213 => uint256 length = 0;
2023-03-neotokyo/contracts/s1/vaultBox.sol::1491 => for (uint16 i = 0; i < proof.length; i++) {
2023-03-neotokyo/contracts/s1/vaultBox.sol::1501 => for(uint i = 0; i < _rootHash.length; i++)
2023-03-neotokyo/contracts/s2/NTOuterCitizenDeploy.sol::232 => uint256 length = 0;
2023-03-neotokyo/contracts/s2/NTOuterIdentity.sol::217 => uint256 length = 0;
2023-03-neotokyo/contracts/s2/NTOuterIdentity.sol::1537 => for (uint16 i = 0; i < proof.length; i++) {
2023-03-neotokyo/contracts/s2/NTOuterIdentity.sol::1547 => for(uint i = 0; i < _rootHash.length; i++)
2023-03-neotokyo/contracts/s2/NTS2Items.sol::216 => uint256 length = 0;
2023-03-neotokyo/contracts/s2/NTS2Items.sol::1385 => for (uint16 i = 0; i < proof.length; i++) {
2023-03-neotokyo/contracts/s2/NTS2Items.sol::1395 => for(uint i = 0; i < _rootHash.length; i++)
2023-03-neotokyo/contracts/s2/NTS2LandDeploy.sol::216 => uint256 length = 0;
2023-03-neotokyo/contracts/s2/NTS2LandDeploy.sol::1358 => for (uint16 i = 0; i < proof.length; i++) {
2023-03-neotokyo/contracts/s2/NTS2LandDeploy.sol::1368 => for(uint i = 0; i < _rootHash.length; i++)
```

## [G-02] Cache Array Length Outside of Loop

Caching the array length outside a loop saves reading it on each iteration, as long as the array's length is not changed during the loop.

```
2023-03-neotokyo/contracts/s1/BYTESContract.sol::1000 => for (uint256 i = 0; i < bytes(_str).length; i++) {
2023-03-neotokyo/contracts/s1/NTItems.sol::1568 => for (uint16 i = 0; i < proof.length; i++) {
2023-03-neotokyo/contracts/s1/NTItems.sol::1578 => for(uint i = 0; i < _rootHash.length; i++)
2023-03-neotokyo/contracts/s1/NTLandDeploy.sol::1405 => for (uint16 i = 0; i < proof.length; i++) {
2023-03-neotokyo/contracts/s1/NTLandDeploy.sol::1415 => for(uint i = 0; i < _rootHash.length; i++)
2023-03-neotokyo/contracts/s1/beckLoot.sol::1527 => for (uint16 i = 0; i < proof.length; i++) {
2023-03-neotokyo/contracts/s1/beckLoot.sol::1537 => for(uint i = 0; i < _rootHash.length; i++)
2023-03-neotokyo/contracts/s1/vaultBox.sol::1491 => for (uint16 i = 0; i < proof.length; i++) {
2023-03-neotokyo/contracts/s1/vaultBox.sol::1501 => for(uint i = 0; i < _rootHash.length; i++)
2023-03-neotokyo/contracts/s2/NTOuterIdentity.sol::1537 => for (uint16 i = 0; i < proof.length; i++) {
2023-03-neotokyo/contracts/s2/NTOuterIdentity.sol::1547 => for(uint i = 0; i < _rootHash.length; i++)
2023-03-neotokyo/contracts/s2/NTS2Items.sol::1385 => for (uint16 i = 0; i < proof.length; i++) {
2023-03-neotokyo/contracts/s2/NTS2Items.sol::1395 => for(uint i = 0; i < _rootHash.length; i++)
2023-03-neotokyo/contracts/s2/NTS2LandDeploy.sol::1358 => for (uint16 i = 0; i < proof.length; i++) {
2023-03-neotokyo/contracts/s2/NTS2LandDeploy.sol::1368 => for(uint i = 0; i < _rootHash.length; i++)
2023-03-neotokyo/contracts/staking/NeoTokyoStaker.sol::717 => for (uint256 i; i < _stakerS1Position[_staker].length; ) {
2023-03-neotokyo/contracts/staking/NeoTokyoStaker.sol::734 => for (uint256 i; i < _stakerS2Position[_staker].length; ) {
2023-03-neotokyo/contracts/staking/NeoTokyoStaker.sol::1279 => for (uint256 i; i < _stakerS1Position[_recipient].length; ) {
2023-03-neotokyo/contracts/staking/NeoTokyoStaker.sol::1288 => for (uint256 i; i < _stakerS2Position[_recipient].length; ) {
2023-03-neotokyo/contracts/staking/NeoTokyoStaker.sol::1499 => for (uint256 stakedIndex; stakedIndex < oldPosition.length; ) {
2023-03-neotokyo/contracts/staking/NeoTokyoStaker.sol::1564 => for (uint256 stakedIndex; stakedIndex < oldPosition.length; ) {
2023-03-neotokyo/contracts/staking/NeoTokyoStaker.sol::1742 => for (uint256 i; i < _timelockIds.length; ) {
2023-03-neotokyo/contracts/staking/NeoTokyoStaker.sol::1765 => for (uint256 i; i < _citizenRewardRates.length; ) {
2023-03-neotokyo/contracts/staking/NeoTokyoStaker.sol::1787 => for (uint256 i; i < _identityCreditYields.length; ) {
2023-03-neotokyo/contracts/staking/NeoTokyoStaker.sol::1806 => for (uint256 i; i < _vaultCreditMultipliers.length; ) {
2023-03-neotokyo/contracts/staking/NeoTokyoStaker.sol::1822 => for (uint256 i; i < _inputs.length; ) {
```

## [G-03] Using > 0 costs more gas than != 0 when used on a uint in a require() statement

When dealing with unsigned integer types, comparisons with != 0 are cheaper then with > 0. This change saves 6 gas per instance

```
2023-03-neotokyo/contracts/s1/BYTESContract.sol::388 => require(b > 0, errorMessage);
2023-03-neotokyo/contracts/s1/NTItems.sol::1506 => require(boxTokenId > 0 && boxTokenId < 2501, "That is not a claimable item");
2023-03-neotokyo/contracts/s1/NTItems.sol::1549 => require(tokenId > 0 && tokenId < 2501, "Token ID invalid");
2023-03-neotokyo/contracts/s1/NTLandDeploy.sol::1340 => require(vaultTokenId > 0 && vaultTokenId < 2501, "That vault cannot claim land");
2023-03-neotokyo/contracts/s1/NTLandDeploy.sol::1374 => require(tokenId > 0 && tokenId < 2501, "Token ID invalid");
2023-03-neotokyo/contracts/s1/beckLoot.sol::1489 => require(tokenId > 0 && tokenId < 2001, "Token ID invalid");
2023-03-neotokyo/contracts/s1/beckLoot.sol::1496 => require(tokenId > 0 && tokenId < 2001, "Token ID invalid");
2023-03-neotokyo/contracts/s1/vaultBox.sol::1425 => require(tokenId > 0 && tokenId < 2501, "Token ID invalid");
2023-03-neotokyo/contracts/s1/vaultBox.sol::1428 => require(identityTokenId > 0, "You have no identities capable of minting");
2023-03-neotokyo/contracts/s1/vaultBox.sol::1442 => require(tokenId > 0 && tokenId < 2501, "Token ID invalid");
2023-03-neotokyo/contracts/s1/vaultBox.sol::1453 => require(tokenId > 0 && tokenId < 2501, "Token ID invalid");
2023-03-neotokyo/contracts/s1/vaultBox.sol::1460 => require(tokenId > 0 && tokenId < 2501, "Token ID invalid");
2023-03-neotokyo/contracts/s2/NTS2Items.sol::1342 => require(identityTokenId > 0 && identityTokenId < 4501, "That is not a claimable item");
2023-03-neotokyo/contracts/s2/NTS2Items.sol::1366 => require(tokenId > 0 && tokenId < 4501, "Token ID invalid");
2023-03-neotokyo/contracts/s2/NTS2LandDeploy.sol::1339 => require(tokenId > 0 && tokenId < 4501, "Token ID invalid");
```

## [G-04] Long Revert Strings

Shortening revert strings to fit in 32 bytes will decrease gas costs for deployment and gas costs when the revert condition has been met.

If the contract(s) in scope allow using Solidity >=0.8.4, consider using Custom Errors as they are more gas efficient while allowing developers to describe the error in detail using NatSpec.

```
2023-03-neotokyo/contracts/s1/BYTESContract.sol::87 => require(success, "Address: unable to send value, recipient may have reverted");
2023-03-neotokyo/contracts/s1/BYTESContract.sol::144 => require(address(this).balance >= value, "Address: insufficient balance for call");
2023-03-neotokyo/contracts/s1/BYTESContract.sol::354 => require(c / a == b, "SafeMath: multiplication overflow");
2023-03-neotokyo/contracts/s1/BYTESContract.sol::484 => require(newOwner != address(0), "Ownable: new owner is the zero address");
2023-03-neotokyo/contracts/s1/BYTESContract.sol::697 => require(sender != address(0), "ERC20: transfer from the zero address");
2023-03-neotokyo/contracts/s1/BYTESContract.sol::698 => require(recipient != address(0), "ERC20: transfer to the zero address");
2023-03-neotokyo/contracts/s1/BYTESContract.sol::738 => require(account != address(0), "ERC20: burn from the zero address");
2023-03-neotokyo/contracts/s1/BYTESContract.sol::761 => require(owner != address(0), "ERC20: approve from the zero address");
2023-03-neotokyo/contracts/s1/BYTESContract.sol::762 => require(spender != address(0), "ERC20: approve to the zero address");
2023-03-neotokyo/contracts/s1/BYTESContract.sol::825 => require(identityBoxOpened[identityTokenId] == 0, "That identity has already opened a box");
2023-03-neotokyo/contracts/s1/BYTESContract.sol::987 => require(adminContracts[msg.sender], "You are not approved to burn tokens");
2023-03-neotokyo/contracts/s1/NTCitizenDeploy.sol::337 => require(newOwner != address(0), "Ownable: new owner is the zero address");
2023-03-neotokyo/contracts/s1/NTCitizenDeploy.sol::509 => require(success, "Address: unable to send value, recipient may have reverted");
2023-03-neotokyo/contracts/s1/NTCitizenDeploy.sol::579 => require(address(this).balance >= value, "Address: insufficient balance for call");
2023-03-neotokyo/contracts/s1/NTCitizenDeploy.sol::607 => require(isContract(target), "Address: static call to non-contract");
2023-03-neotokyo/contracts/s1/NTCitizenDeploy.sol::634 => require(isContract(target), "Address: delegate call to non-contract");
2023-03-neotokyo/contracts/s1/NTCitizenDeploy.sol::739 => require(owner != address(0), "ERC721: balance query for the zero address");
2023-03-neotokyo/contracts/s1/NTCitizenDeploy.sol::748 => require(owner != address(0), "ERC721: owner query for nonexistent token");
2023-03-neotokyo/contracts/s1/NTCitizenDeploy.sol::770 => require(_exists(tokenId), "ERC721Metadata: URI query for nonexistent token");
2023-03-neotokyo/contracts/s1/NTCitizenDeploy.sol::790 => require(to != owner, "ERC721: approval to current owner");
2023-03-neotokyo/contracts/s1/NTCitizenDeploy.sol::804 => require(_exists(tokenId), "ERC721: approved query for nonexistent token");
2023-03-neotokyo/contracts/s1/NTCitizenDeploy.sol::835 => require(_isApprovedOrOwner(_msgSender(), tokenId), "ERC721: transfer caller is not owner nor approved");
2023-03-neotokyo/contracts/s1/NTCitizenDeploy.sol::860 => require(_isApprovedOrOwner(_msgSender(), tokenId), "ERC721: transfer caller is not owner nor approved");
2023-03-neotokyo/contracts/s1/NTCitizenDeploy.sol::889 => require(_checkOnERC721Received(from, to, tokenId, _data), "ERC721: transfer to non ERC721Receiver implementer");
2023-03-neotokyo/contracts/s1/NTCitizenDeploy.sol::912 => require(_exists(tokenId), "ERC721: operator query for nonexistent token");
2023-03-neotokyo/contracts/s1/NTCitizenDeploy.sol::1011 => require(ERC721.ownerOf(tokenId) == from, "ERC721: transfer of token that is not own");
2023-03-neotokyo/contracts/s1/NTCitizenDeploy.sol::1012 => require(to != address(0), "ERC721: transfer to the zero address");
2023-03-neotokyo/contracts/s1/NTCitizenDeploy.sol::1145 => require(index < ERC721.balanceOf(owner), "ERC721Enumerable: owner index out of bounds");
2023-03-neotokyo/contracts/s1/NTCitizenDeploy.sol::1160 => require(index < ERC721Enumerable.totalSupply(), "ERC721Enumerable: global index out of bounds");
2023-03-neotokyo/contracts/s1/NTCitizenDeploy.sol::1342 => require(_exists(tokenId), "ERC721Metadata: URI query for nonexistent token");
2023-03-neotokyo/contracts/s1/NTCitizenDeploy.sol::1411 => require(citizenMintActive, "Uploading is not currently active");
2023-03-neotokyo/contracts/s1/NTCitizenDeploy.sol::1412 => require(identityValidated(identityId), "You are not the owner of that identity");
2023-03-neotokyo/contracts/s1/NTCitizenDeploy.sol::1413 => require(itemCacheValidated(itemCacheId), "You are not the owner of that item cache");
2023-03-neotokyo/contracts/s1/NTCitizenDeploy.sol::1414 => require(landDeedValidated(landDeedId), "You are not the owner of that land deed");
2023-03-neotokyo/contracts/s1/NTCitizenDeploy.sol::1423 => require(vaultValidated(vaultId), "You are not the owner of that vault box");
2023-03-neotokyo/contracts/s1/NTItems.sol::317 => require(newOwner != address(0), "Ownable: new owner is the zero address");
2023-03-neotokyo/contracts/s1/NTItems.sol::489 => require(success, "Address: unable to send value, recipient may have reverted");
2023-03-neotokyo/contracts/s1/NTItems.sol::559 => require(address(this).balance >= value, "Address: insufficient balance for call");
2023-03-neotokyo/contracts/s1/NTItems.sol::587 => require(isContract(target), "Address: static call to non-contract");
2023-03-neotokyo/contracts/s1/NTItems.sol::614 => require(isContract(target), "Address: delegate call to non-contract");
2023-03-neotokyo/contracts/s1/NTItems.sol::719 => require(owner != address(0), "ERC721: balance query for the zero address");
2023-03-neotokyo/contracts/s1/NTItems.sol::728 => require(owner != address(0), "ERC721: owner query for nonexistent token");
2023-03-neotokyo/contracts/s1/NTItems.sol::750 => require(_exists(tokenId), "ERC721Metadata: URI query for nonexistent token");
2023-03-neotokyo/contracts/s1/NTItems.sol::770 => require(to != owner, "ERC721: approval to current owner");
2023-03-neotokyo/contracts/s1/NTItems.sol::784 => require(_exists(tokenId), "ERC721: approved query for nonexistent token");
2023-03-neotokyo/contracts/s1/NTItems.sol::815 => require(_isApprovedOrOwner(_msgSender(), tokenId), "ERC721: transfer caller is not owner nor approved");
2023-03-neotokyo/contracts/s1/NTItems.sol::840 => require(_isApprovedOrOwner(_msgSender(), tokenId), "ERC721: transfer caller is not owner nor approved");
2023-03-neotokyo/contracts/s1/NTItems.sol::869 => require(_checkOnERC721Received(from, to, tokenId, _data), "ERC721: transfer to non ERC721Receiver implementer");
2023-03-neotokyo/contracts/s1/NTItems.sol::892 => require(_exists(tokenId), "ERC721: operator query for nonexistent token");
2023-03-neotokyo/contracts/s1/NTItems.sol::991 => require(ERC721.ownerOf(tokenId) == from, "ERC721: transfer of token that is not own");
2023-03-neotokyo/contracts/s1/NTItems.sol::992 => require(to != address(0), "ERC721: transfer to the zero address");
2023-03-neotokyo/contracts/s1/NTItems.sol::1125 => require(index < ERC721.balanceOf(owner), "ERC721Enumerable: owner index out of bounds");
2023-03-neotokyo/contracts/s1/NTItems.sol::1140 => require(index < ERC721Enumerable.totalSupply(), "ERC721Enumerable: global index out of bounds");
2023-03-neotokyo/contracts/s1/NTItems.sol::1444 => require(_exists(tokenId), "ERC721Metadata: URI query for nonexistent token");
2023-03-neotokyo/contracts/s1/NTItems.sol::1455 => require(_exists(tokenId), "ERC721Metadata: URI query for nonexistent token");
2023-03-neotokyo/contracts/s1/NTItems.sol::1465 => require(_exists(tokenId), "ERC721Metadata: URI query for nonexistent token");
2023-03-neotokyo/contracts/s1/NTItems.sol::1475 => require(_exists(tokenId), "ERC721Metadata: URI query for nonexistent token");
2023-03-neotokyo/contracts/s1/NTItems.sol::1493 => require(_exists(tokenId), "ERC721Metadata: URI query for nonexistent token");
2023-03-neotokyo/contracts/s1/NTItems.sol::1508 => require(boxValidated(boxTokenId), "You are not the owner of that vault box");
2023-03-neotokyo/contracts/s1/NTLandDeploy.sol::317 => require(newOwner != address(0), "Ownable: new owner is the zero address");
2023-03-neotokyo/contracts/s1/NTLandDeploy.sol::489 => require(success, "Address: unable to send value, recipient may have reverted");
2023-03-neotokyo/contracts/s1/NTLandDeploy.sol::559 => require(address(this).balance >= value, "Address: insufficient balance for call");
2023-03-neotokyo/contracts/s1/NTLandDeploy.sol::587 => require(isContract(target), "Address: static call to non-contract");
2023-03-neotokyo/contracts/s1/NTLandDeploy.sol::614 => require(isContract(target), "Address: delegate call to non-contract");
2023-03-neotokyo/contracts/s1/NTLandDeploy.sol::719 => require(owner != address(0), "ERC721: balance query for the zero address");
2023-03-neotokyo/contracts/s1/NTLandDeploy.sol::728 => require(owner != address(0), "ERC721: owner query for nonexistent token");
2023-03-neotokyo/contracts/s1/NTLandDeploy.sol::750 => require(_exists(tokenId), "ERC721Metadata: URI query for nonexistent token");
2023-03-neotokyo/contracts/s1/NTLandDeploy.sol::770 => require(to != owner, "ERC721: approval to current owner");
2023-03-neotokyo/contracts/s1/NTLandDeploy.sol::784 => require(_exists(tokenId), "ERC721: approved query for nonexistent token");
2023-03-neotokyo/contracts/s1/NTLandDeploy.sol::815 => require(_isApprovedOrOwner(_msgSender(), tokenId), "ERC721: transfer caller is not owner nor approved");
2023-03-neotokyo/contracts/s1/NTLandDeploy.sol::840 => require(_isApprovedOrOwner(_msgSender(), tokenId), "ERC721: transfer caller is not owner nor approved");
2023-03-neotokyo/contracts/s1/NTLandDeploy.sol::869 => require(_checkOnERC721Received(from, to, tokenId, _data), "ERC721: transfer to non ERC721Receiver implementer");
2023-03-neotokyo/contracts/s1/NTLandDeploy.sol::892 => require(_exists(tokenId), "ERC721: operator query for nonexistent token");
2023-03-neotokyo/contracts/s1/NTLandDeploy.sol::991 => require(ERC721.ownerOf(tokenId) == from, "ERC721: transfer of token that is not own");
2023-03-neotokyo/contracts/s1/NTLandDeploy.sol::992 => require(to != address(0), "ERC721: transfer to the zero address");
2023-03-neotokyo/contracts/s1/NTLandDeploy.sol::1125 => require(index < ERC721.balanceOf(owner), "ERC721Enumerable: owner index out of bounds");
2023-03-neotokyo/contracts/s1/NTLandDeploy.sol::1140 => require(index < ERC721Enumerable.totalSupply(), "ERC721Enumerable: global index out of bounds");
2023-03-neotokyo/contracts/s1/NTLandDeploy.sol::1298 => require(_exists(tokenId), "ERC721Metadata: URI query for nonexistent token");
2023-03-neotokyo/contracts/s1/NTLandDeploy.sol::1328 => require(_exists(tokenId), "ERC721Metadata: URI query for nonexistent token");
2023-03-neotokyo/contracts/s1/NTLandDeploy.sol::1341 => require(whitelistValidated(toString(vaultTokenId), spotOnLeaderboard, spotInWhitelist, proof), "That vault cannot claim that item");
2023-03-neotokyo/contracts/s1/NTLandDeploy.sol::1342 => require(vaultValidated(vaultTokenId), "You are not the owner of that vault box");
2023-03-neotokyo/contracts/s1/NTLandDeploy.sol::1343 => require(identityValidated(identityTokenId), "You are not the owner of that identity");
2023-03-neotokyo/contracts/s1/NTLandDeploy.sol::1344 => require(itemCacheValidated(itemCacheTokenId), "You are not the owner of that item cache");
2023-03-neotokyo/contracts/s1/beckLoot.sol::314 => require(newOwner != address(0), "Ownable: new owner is the zero address");
2023-03-neotokyo/contracts/s1/beckLoot.sol::486 => require(success, "Address: unable to send value, recipient may have reverted");
2023-03-neotokyo/contracts/s1/beckLoot.sol::556 => require(address(this).balance >= value, "Address: insufficient balance for call");
2023-03-neotokyo/contracts/s1/beckLoot.sol::584 => require(isContract(target), "Address: static call to non-contract");
2023-03-neotokyo/contracts/s1/beckLoot.sol::611 => require(isContract(target), "Address: delegate call to non-contract");
2023-03-neotokyo/contracts/s1/beckLoot.sol::716 => require(owner != address(0), "ERC721: balance query for the zero address");
2023-03-neotokyo/contracts/s1/beckLoot.sol::725 => require(owner != address(0), "ERC721: owner query for nonexistent token");
2023-03-neotokyo/contracts/s1/beckLoot.sol::747 => require(_exists(tokenId), "ERC721Metadata: URI query for nonexistent token");
2023-03-neotokyo/contracts/s1/beckLoot.sol::767 => require(to != owner, "ERC721: approval to current owner");
2023-03-neotokyo/contracts/s1/beckLoot.sol::781 => require(_exists(tokenId), "ERC721: approved query for nonexistent token");
2023-03-neotokyo/contracts/s1/beckLoot.sol::812 => require(_isApprovedOrOwner(_msgSender(), tokenId), "ERC721: transfer caller is not owner nor approved");
2023-03-neotokyo/contracts/s1/beckLoot.sol::837 => require(_isApprovedOrOwner(_msgSender(), tokenId), "ERC721: transfer caller is not owner nor approved");
2023-03-neotokyo/contracts/s1/beckLoot.sol::866 => require(_checkOnERC721Received(from, to, tokenId, _data), "ERC721: transfer to non ERC721Receiver implementer");
2023-03-neotokyo/contracts/s1/beckLoot.sol::889 => require(_exists(tokenId), "ERC721: operator query for nonexistent token");
2023-03-neotokyo/contracts/s1/beckLoot.sol::988 => require(ERC721.ownerOf(tokenId) == from, "ERC721: transfer of token that is not own");
2023-03-neotokyo/contracts/s1/beckLoot.sol::989 => require(to != address(0), "ERC721: transfer to the zero address");
2023-03-neotokyo/contracts/s1/beckLoot.sol::1122 => require(index < ERC721.balanceOf(owner), "ERC721Enumerable: owner index out of bounds");
2023-03-neotokyo/contracts/s1/beckLoot.sol::1137 => require(index < ERC721Enumerable.totalSupply(), "ERC721Enumerable: global index out of bounds");
2023-03-neotokyo/contracts/s1/beckLoot.sol::1368 => require(_exists(tokenId), "ERC721Metadata: URI query for nonexistent token");
2023-03-neotokyo/contracts/s1/beckLoot.sol::1378 => require(_exists(tokenId), "ERC721Metadata: URI query for nonexistent token");
2023-03-neotokyo/contracts/s1/beckLoot.sol::1388 => require(_exists(tokenId), "ERC721Metadata: URI query for nonexistent token");
2023-03-neotokyo/contracts/s1/beckLoot.sol::1398 => require(_exists(tokenId), "ERC721Metadata: URI query for nonexistent token");
2023-03-neotokyo/contracts/s1/beckLoot.sol::1408 => require(_exists(tokenId), "ERC721Metadata: URI query for nonexistent token");
2023-03-neotokyo/contracts/s1/beckLoot.sol::1418 => require(_exists(tokenId), "ERC721Metadata: URI query for nonexistent token");
2023-03-neotokyo/contracts/s1/beckLoot.sol::1428 => require(_exists(tokenId), "ERC721Metadata: URI query for nonexistent token");
2023-03-neotokyo/contracts/s1/beckLoot.sol::1438 => require(_exists(tokenId), "ERC721Metadata: URI query for nonexistent token");
2023-03-neotokyo/contracts/s1/beckLoot.sol::1448 => require(_exists(tokenId), "ERC721Metadata: URI query for nonexistent token");
2023-03-neotokyo/contracts/s1/beckLoot.sol::1458 => require(_exists(tokenId), "ERC721Metadata: URI query for nonexistent token");
2023-03-neotokyo/contracts/s1/beckLoot.sol::1468 => require(_exists(tokenId), "ERC721Metadata: URI query for nonexistent token");
2023-03-neotokyo/contracts/s1/beckLoot.sol::1478 => require(_exists(tokenId), "ERC721Metadata: URI query for nonexistent token");
2023-03-neotokyo/contracts/s1/beckLoot.sol::1490 => require(saleActive, "Public claiming is currently closed");
2023-03-neotokyo/contracts/s1/vaultBox.sol::314 => require(newOwner != address(0), "Ownable: new owner is the zero address");
2023-03-neotokyo/contracts/s1/vaultBox.sol::486 => require(success, "Address: unable to send value, recipient may have reverted");
2023-03-neotokyo/contracts/s1/vaultBox.sol::556 => require(address(this).balance >= value, "Address: insufficient balance for call");
2023-03-neotokyo/contracts/s1/vaultBox.sol::584 => require(isContract(target), "Address: static call to non-contract");
2023-03-neotokyo/contracts/s1/vaultBox.sol::611 => require(isContract(target), "Address: delegate call to non-contract");
2023-03-neotokyo/contracts/s1/vaultBox.sol::716 => require(owner != address(0), "ERC721: balance query for the zero address");
2023-03-neotokyo/contracts/s1/vaultBox.sol::725 => require(owner != address(0), "ERC721: owner query for nonexistent token");
2023-03-neotokyo/contracts/s1/vaultBox.sol::747 => require(_exists(tokenId), "ERC721Metadata: URI query for nonexistent token");
2023-03-neotokyo/contracts/s1/vaultBox.sol::767 => require(to != owner, "ERC721: approval to current owner");
2023-03-neotokyo/contracts/s1/vaultBox.sol::781 => require(_exists(tokenId), "ERC721: approved query for nonexistent token");
2023-03-neotokyo/contracts/s1/vaultBox.sol::812 => require(_isApprovedOrOwner(_msgSender(), tokenId), "ERC721: transfer caller is not owner nor approved");
2023-03-neotokyo/contracts/s1/vaultBox.sol::837 => require(_isApprovedOrOwner(_msgSender(), tokenId), "ERC721: transfer caller is not owner nor approved");
2023-03-neotokyo/contracts/s1/vaultBox.sol::866 => require(_checkOnERC721Received(from, to, tokenId, _data), "ERC721: transfer to non ERC721Receiver implementer");
2023-03-neotokyo/contracts/s1/vaultBox.sol::889 => require(_exists(tokenId), "ERC721: operator query for nonexistent token");
2023-03-neotokyo/contracts/s1/vaultBox.sol::988 => require(ERC721.ownerOf(tokenId) == from, "ERC721: transfer of token that is not own");
2023-03-neotokyo/contracts/s1/vaultBox.sol::989 => require(to != address(0), "ERC721: transfer to the zero address");
2023-03-neotokyo/contracts/s1/vaultBox.sol::1122 => require(index < ERC721.balanceOf(owner), "ERC721Enumerable: owner index out of bounds");
2023-03-neotokyo/contracts/s1/vaultBox.sol::1137 => require(index < ERC721Enumerable.totalSupply(), "ERC721Enumerable: global index out of bounds");
2023-03-neotokyo/contracts/s1/vaultBox.sol::1355 => require(_exists(tokenId), "ERC721Metadata: URI query for nonexistent token");
2023-03-neotokyo/contracts/s1/vaultBox.sol::1366 => require(_exists(tokenId), "ERC721Metadata: URI query for nonexistent token");
2023-03-neotokyo/contracts/s1/vaultBox.sol::1376 => require(_exists(tokenId), "ERC721Metadata: URI query for nonexistent token");
2023-03-neotokyo/contracts/s1/vaultBox.sol::1386 => require(_exists(tokenId), "ERC721Metadata: URI query for nonexistent token");
2023-03-neotokyo/contracts/s1/vaultBox.sol::1404 => require(_exists(tokenId), "ERC721Metadata: URI query for nonexistent token");
2023-03-neotokyo/contracts/s1/vaultBox.sol::1426 => require(saleActive, "Holder claiming is currently closed");
2023-03-neotokyo/contracts/s1/vaultBox.sol::1428 => require(identityTokenId > 0, "You have no identities capable of minting");
2023-03-neotokyo/contracts/s1/vaultBox.sol::1454 => require(openClaimActive, "Public claiming is currently closed");
2023-03-neotokyo/contracts/s2/NTOuterCitizenDeploy.sol::333 => require(newOwner != address(0), "Ownable: new owner is the zero address");
2023-03-neotokyo/contracts/s2/NTOuterCitizenDeploy.sol::505 => require(success, "Address: unable to send value, recipient may have reverted");
2023-03-neotokyo/contracts/s2/NTOuterCitizenDeploy.sol::575 => require(address(this).balance >= value, "Address: insufficient balance for call");
2023-03-neotokyo/contracts/s2/NTOuterCitizenDeploy.sol::603 => require(isContract(target), "Address: static call to non-contract");
2023-03-neotokyo/contracts/s2/NTOuterCitizenDeploy.sol::630 => require(isContract(target), "Address: delegate call to non-contract");
2023-03-neotokyo/contracts/s2/NTOuterCitizenDeploy.sol::735 => require(owner != address(0), "ERC721: balance query for the zero address");
2023-03-neotokyo/contracts/s2/NTOuterCitizenDeploy.sol::744 => require(owner != address(0), "ERC721: owner query for nonexistent token");
2023-03-neotokyo/contracts/s2/NTOuterCitizenDeploy.sol::766 => require(_exists(tokenId), "ERC721Metadata: URI query for nonexistent token");
2023-03-neotokyo/contracts/s2/NTOuterCitizenDeploy.sol::786 => require(to != owner, "ERC721: approval to current owner");
2023-03-neotokyo/contracts/s2/NTOuterCitizenDeploy.sol::800 => require(_exists(tokenId), "ERC721: approved query for nonexistent token");
2023-03-neotokyo/contracts/s2/NTOuterCitizenDeploy.sol::831 => require(_isApprovedOrOwner(_msgSender(), tokenId), "ERC721: transfer caller is not owner nor approved");
2023-03-neotokyo/contracts/s2/NTOuterCitizenDeploy.sol::856 => require(_isApprovedOrOwner(_msgSender(), tokenId), "ERC721: transfer caller is not owner nor approved");
2023-03-neotokyo/contracts/s2/NTOuterCitizenDeploy.sol::885 => require(_checkOnERC721Received(from, to, tokenId, _data), "ERC721: transfer to non ERC721Receiver implementer");
2023-03-neotokyo/contracts/s2/NTOuterCitizenDeploy.sol::908 => require(_exists(tokenId), "ERC721: operator query for nonexistent token");
2023-03-neotokyo/contracts/s2/NTOuterCitizenDeploy.sol::1007 => require(ERC721.ownerOf(tokenId) == from, "ERC721: transfer of token that is not own");
2023-03-neotokyo/contracts/s2/NTOuterCitizenDeploy.sol::1008 => require(to != address(0), "ERC721: transfer to the zero address");
2023-03-neotokyo/contracts/s2/NTOuterCitizenDeploy.sol::1141 => require(index < ERC721.balanceOf(owner), "ERC721Enumerable: owner index out of bounds");
2023-03-neotokyo/contracts/s2/NTOuterCitizenDeploy.sol::1156 => require(index < ERC721Enumerable.totalSupply(), "ERC721Enumerable: global index out of bounds");
2023-03-neotokyo/contracts/s2/NTOuterCitizenDeploy.sol::1333 => require(_exists(tokenId), "ERC721Metadata: URI query for nonexistent token");
2023-03-neotokyo/contracts/s2/NTOuterCitizenDeploy.sol::1353 => require(citizenMintActive, "Uploading is not currently active");
2023-03-neotokyo/contracts/s2/NTOuterCitizenDeploy.sol::1354 => require(identityValidated(identityId), "You are not the owner of that identity");
2023-03-neotokyo/contracts/s2/NTOuterCitizenDeploy.sol::1355 => require(itemCacheValidated(itemCacheId), "You are not the owner of that item cache");
2023-03-neotokyo/contracts/s2/NTOuterCitizenDeploy.sol::1356 => require(landDeedValidated(landDeedId), "You are not the owner of that land deed");
2023-03-neotokyo/contracts/s2/NTOuterIdentity.sol::318 => require(newOwner != address(0), "Ownable: new owner is the zero address");
2023-03-neotokyo/contracts/s2/NTOuterIdentity.sol::490 => require(success, "Address: unable to send value, recipient may have reverted");
2023-03-neotokyo/contracts/s2/NTOuterIdentity.sol::560 => require(address(this).balance >= value, "Address: insufficient balance for call");
2023-03-neotokyo/contracts/s2/NTOuterIdentity.sol::588 => require(isContract(target), "Address: static call to non-contract");
2023-03-neotokyo/contracts/s2/NTOuterIdentity.sol::615 => require(isContract(target), "Address: delegate call to non-contract");
2023-03-neotokyo/contracts/s2/NTOuterIdentity.sol::720 => require(owner != address(0), "ERC721: balance query for the zero address");
2023-03-neotokyo/contracts/s2/NTOuterIdentity.sol::729 => require(owner != address(0), "ERC721: owner query for nonexistent token");
2023-03-neotokyo/contracts/s2/NTOuterIdentity.sol::751 => require(_exists(tokenId), "ERC721Metadata: URI query for nonexistent token");
2023-03-neotokyo/contracts/s2/NTOuterIdentity.sol::771 => require(to != owner, "ERC721: approval to current owner");
2023-03-neotokyo/contracts/s2/NTOuterIdentity.sol::785 => require(_exists(tokenId), "ERC721: approved query for nonexistent token");
2023-03-neotokyo/contracts/s2/NTOuterIdentity.sol::816 => require(_isApprovedOrOwner(_msgSender(), tokenId), "ERC721: transfer caller is not owner nor approved");
2023-03-neotokyo/contracts/s2/NTOuterIdentity.sol::841 => require(_isApprovedOrOwner(_msgSender(), tokenId), "ERC721: transfer caller is not owner nor approved");
2023-03-neotokyo/contracts/s2/NTOuterIdentity.sol::870 => require(_checkOnERC721Received(from, to, tokenId, _data), "ERC721: transfer to non ERC721Receiver implementer");
2023-03-neotokyo/contracts/s2/NTOuterIdentity.sol::893 => require(_exists(tokenId), "ERC721: operator query for nonexistent token");
2023-03-neotokyo/contracts/s2/NTOuterIdentity.sol::992 => require(ERC721.ownerOf(tokenId) == from, "ERC721: transfer of token that is not own");
2023-03-neotokyo/contracts/s2/NTOuterIdentity.sol::993 => require(to != address(0), "ERC721: transfer to the zero address");
2023-03-neotokyo/contracts/s2/NTOuterIdentity.sol::1126 => require(index < ERC721.balanceOf(owner), "ERC721Enumerable: owner index out of bounds");
2023-03-neotokyo/contracts/s2/NTOuterIdentity.sol::1141 => require(index < ERC721Enumerable.totalSupply(), "ERC721Enumerable: global index out of bounds");
2023-03-neotokyo/contracts/s2/NTOuterIdentity.sol::1339 => require(_exists(tokenId), "ERC721Metadata: URI query for nonexistent token");
2023-03-neotokyo/contracts/s2/NTOuterIdentity.sol::1349 => require(_exists(tokenId), "ERC721Metadata: URI query for nonexistent token");
2023-03-neotokyo/contracts/s2/NTOuterIdentity.sol::1359 => require(_exists(tokenId), "ERC721Metadata: URI query for nonexistent token");
2023-03-neotokyo/contracts/s2/NTOuterIdentity.sol::1369 => require(_exists(tokenId), "ERC721Metadata: URI query for nonexistent token");
2023-03-neotokyo/contracts/s2/NTOuterIdentity.sol::1379 => require(_exists(tokenId), "ERC721Metadata: URI query for nonexistent token");
2023-03-neotokyo/contracts/s2/NTOuterIdentity.sol::1389 => require(_exists(tokenId), "ERC721Metadata: URI query for nonexistent token");
2023-03-neotokyo/contracts/s2/NTOuterIdentity.sol::1399 => require(_exists(tokenId), "ERC721Metadata: URI query for nonexistent token");
2023-03-neotokyo/contracts/s2/NTOuterIdentity.sol::1409 => require(_exists(tokenId), "ERC721Metadata: URI query for nonexistent token");
2023-03-neotokyo/contracts/s2/NTOuterIdentity.sol::1419 => require(_exists(tokenId), "ERC721Metadata: URI query for nonexistent token");
2023-03-neotokyo/contracts/s2/NTOuterIdentity.sol::1429 => require(_exists(tokenId), "ERC721Metadata: URI query for nonexistent token");
2023-03-neotokyo/contracts/s2/NTOuterIdentity.sol::1439 => require(_exists(tokenId), "ERC721Metadata: URI query for nonexistent token");
2023-03-neotokyo/contracts/s2/NTOuterIdentity.sol::1449 => require(_exists(tokenId), "ERC721Metadata: URI query for nonexistent token");
2023-03-neotokyo/contracts/s2/NTS2Items.sol::317 => require(newOwner != address(0), "Ownable: new owner is the zero address");
2023-03-neotokyo/contracts/s2/NTS2Items.sol::489 => require(success, "Address: unable to send value, recipient may have reverted");
2023-03-neotokyo/contracts/s2/NTS2Items.sol::559 => require(address(this).balance >= value, "Address: insufficient balance for call");
2023-03-neotokyo/contracts/s2/NTS2Items.sol::587 => require(isContract(target), "Address: static call to non-contract");
2023-03-neotokyo/contracts/s2/NTS2Items.sol::614 => require(isContract(target), "Address: delegate call to non-contract");
2023-03-neotokyo/contracts/s2/NTS2Items.sol::719 => require(owner != address(0), "ERC721: balance query for the zero address");
2023-03-neotokyo/contracts/s2/NTS2Items.sol::728 => require(owner != address(0), "ERC721: owner query for nonexistent token");
2023-03-neotokyo/contracts/s2/NTS2Items.sol::750 => require(_exists(tokenId), "ERC721Metadata: URI query for nonexistent token");
2023-03-neotokyo/contracts/s2/NTS2Items.sol::770 => require(to != owner, "ERC721: approval to current owner");
2023-03-neotokyo/contracts/s2/NTS2Items.sol::784 => require(_exists(tokenId), "ERC721: approved query for nonexistent token");
2023-03-neotokyo/contracts/s2/NTS2Items.sol::815 => require(_isApprovedOrOwner(_msgSender(), tokenId), "ERC721: transfer caller is not owner nor approved");
2023-03-neotokyo/contracts/s2/NTS2Items.sol::840 => require(_isApprovedOrOwner(_msgSender(), tokenId), "ERC721: transfer caller is not owner nor approved");
2023-03-neotokyo/contracts/s2/NTS2Items.sol::869 => require(_checkOnERC721Received(from, to, tokenId, _data), "ERC721: transfer to non ERC721Receiver implementer");
2023-03-neotokyo/contracts/s2/NTS2Items.sol::892 => require(_exists(tokenId), "ERC721: operator query for nonexistent token");
2023-03-neotokyo/contracts/s2/NTS2Items.sol::991 => require(ERC721.ownerOf(tokenId) == from, "ERC721: transfer of token that is not own");
2023-03-neotokyo/contracts/s2/NTS2Items.sol::992 => require(to != address(0), "ERC721: transfer to the zero address");
2023-03-neotokyo/contracts/s2/NTS2Items.sol::1125 => require(index < ERC721.balanceOf(owner), "ERC721Enumerable: owner index out of bounds");
2023-03-neotokyo/contracts/s2/NTS2Items.sol::1140 => require(index < ERC721Enumerable.totalSupply(), "ERC721Enumerable: global index out of bounds");
2023-03-neotokyo/contracts/s2/NTS2Items.sol::1276 => require(_exists(tokenId), "ERC721Metadata: URI query for nonexistent token");
2023-03-neotokyo/contracts/s2/NTS2Items.sol::1287 => require(_exists(tokenId), "ERC721Metadata: URI query for nonexistent token");
2023-03-neotokyo/contracts/s2/NTS2Items.sol::1297 => require(_exists(tokenId), "ERC721Metadata: URI query for nonexistent token");
2023-03-neotokyo/contracts/s2/NTS2Items.sol::1307 => require(_exists(tokenId), "ERC721Metadata: URI query for nonexistent token");
2023-03-neotokyo/contracts/s2/NTS2Items.sol::1329 => require(_exists(tokenId), "ERC721Metadata: URI query for nonexistent token");
2023-03-neotokyo/contracts/s2/NTS2Items.sol::1343 => require(whitelistValidated(identityTokenId, spotOnLeaderboard, spotInWhitelist, proof), "That identity cannot claim that item");
2023-03-neotokyo/contracts/s2/NTS2Items.sol::1344 => require(identityValidated(identityTokenId), "You are not the owner of that identity ");
2023-03-neotokyo/contracts/s2/NTS2Items.sol::1368 => require(_identityClaims[identityId] == 0, "That identity has already claimed");
2023-03-neotokyo/contracts/s2/NTS2LandDeploy.sol::317 => require(newOwner != address(0), "Ownable: new owner is the zero address");
2023-03-neotokyo/contracts/s2/NTS2LandDeploy.sol::489 => require(success, "Address: unable to send value, recipient may have reverted");
2023-03-neotokyo/contracts/s2/NTS2LandDeploy.sol::559 => require(address(this).balance >= value, "Address: insufficient balance for call");
2023-03-neotokyo/contracts/s2/NTS2LandDeploy.sol::587 => require(isContract(target), "Address: static call to non-contract");
2023-03-neotokyo/contracts/s2/NTS2LandDeploy.sol::614 => require(isContract(target), "Address: delegate call to non-contract");
2023-03-neotokyo/contracts/s2/NTS2LandDeploy.sol::719 => require(owner != address(0), "ERC721: balance query for the zero address");
2023-03-neotokyo/contracts/s2/NTS2LandDeploy.sol::728 => require(owner != address(0), "ERC721: owner query for nonexistent token");
2023-03-neotokyo/contracts/s2/NTS2LandDeploy.sol::750 => require(_exists(tokenId), "ERC721Metadata: URI query for nonexistent token");
2023-03-neotokyo/contracts/s2/NTS2LandDeploy.sol::770 => require(to != owner, "ERC721: approval to current owner");
2023-03-neotokyo/contracts/s2/NTS2LandDeploy.sol::784 => require(_exists(tokenId), "ERC721: approved query for nonexistent token");
2023-03-neotokyo/contracts/s2/NTS2LandDeploy.sol::815 => require(_isApprovedOrOwner(_msgSender(), tokenId), "ERC721: transfer caller is not owner nor approved");
2023-03-neotokyo/contracts/s2/NTS2LandDeploy.sol::840 => require(_isApprovedOrOwner(_msgSender(), tokenId), "ERC721: transfer caller is not owner nor approved");
2023-03-neotokyo/contracts/s2/NTS2LandDeploy.sol::869 => require(_checkOnERC721Received(from, to, tokenId, _data), "ERC721: transfer to non ERC721Receiver implementer");
2023-03-neotokyo/contracts/s2/NTS2LandDeploy.sol::892 => require(_exists(tokenId), "ERC721: operator query for nonexistent token");
2023-03-neotokyo/contracts/s2/NTS2LandDeploy.sol::991 => require(ERC721.ownerOf(tokenId) == from, "ERC721: transfer of token that is not own");
2023-03-neotokyo/contracts/s2/NTS2LandDeploy.sol::992 => require(to != address(0), "ERC721: transfer to the zero address");
2023-03-neotokyo/contracts/s2/NTS2LandDeploy.sol::1125 => require(index < ERC721.balanceOf(owner), "ERC721Enumerable: owner index out of bounds");
2023-03-neotokyo/contracts/s2/NTS2LandDeploy.sol::1140 => require(index < ERC721Enumerable.totalSupply(), "ERC721Enumerable: global index out of bounds");
2023-03-neotokyo/contracts/s2/NTS2LandDeploy.sol::1286 => require(_exists(tokenId), "ERC721Metadata: URI query for nonexistent token");
2023-03-neotokyo/contracts/s2/NTS2LandDeploy.sol::1304 => require(_exists(tokenId), "ERC721Metadata: URI query for nonexistent token");
2023-03-neotokyo/contracts/s2/NTS2LandDeploy.sol::1316 => require(whitelistValidated(identityTokenId, spotOnLeaderboard, spotInWhitelist, proof), "That identity cannot claim that land");
2023-03-neotokyo/contracts/s2/NTS2LandDeploy.sol::1317 => require(identityValidated(identityTokenId), "You are not the owner of that identity");
2023-03-neotokyo/contracts/s2/NTS2LandDeploy.sol::1341 => require(_identityClaims[identityId] == 0, "That identity has already claimed");
```

## [G-05] Use Shift Right/Left instead of Division/Multiplication if possible

A division/multiplication by any number x being a power of 2 can be calculated by shifting log2(x) to the right/left.

While the DIV opcode uses 5 gas, the SHR opcode only uses 3 gas. Furthermore, Solidity's division operation also includes a division-by-0 prevention which is bypassed using shifting.

```
2023-03-neotokyo/contracts/staking/BYTES2.sol::152 => treasuryShare = _amount * 2 / 3;
```

## [G-06] Use calldata instead of memory

Use calldata instead of memory for function parameters saves gas if the function argument is only read.

```
2023-03-neotokyo/contracts/s1/BYTESContract.sol::108 => function functionCall(address target, bytes memory data) internal returns (bytes memory) {
2023-03-neotokyo/contracts/s1/BYTESContract.sol::118 => function functionCall(address target, bytes memory data, string memory errorMessage) internal returns (bytes memory) {
2023-03-neotokyo/contracts/s1/BYTESContract.sol::133 => function functionCallWithValue(address target, bytes memory data, uint256 value) internal returns (bytes memory) {
2023-03-neotokyo/contracts/s1/BYTESContract.sol::143 => function functionCallWithValue(address target, bytes memory data, uint256 value, string memory errorMessage) internal returns (bytes memory) {
2023-03-neotokyo/contracts/s1/BYTESContract.sol::148 => function _functionCallWithValue(address target, bytes memory data, uint256 weiValue, string memory errorMessage) private returns (bytes memory) {
2023-03-neotokyo/contracts/s1/BYTESContract.sol::328 => function sub(uint256 a, uint256 b, string memory errorMessage) internal pure returns (uint256) {
2023-03-neotokyo/contracts/s1/BYTESContract.sol::387 => function div(uint256 a, uint256 b, string memory errorMessage) internal pure returns (uint256) {
2023-03-neotokyo/contracts/s1/BYTESContract.sol::423 => function mod(uint256 a, uint256 b, string memory errorMessage) internal pure returns (uint256) {
2023-03-neotokyo/contracts/s1/BYTESContract.sol::999 => function strToUint(string memory _str) public pure returns(uint256 res, bool err) {
2023-03-neotokyo/contracts/s1/NTCitizenDeploy.sol::530 => function functionCall(address target, bytes memory data) internal returns (bytes memory) {
2023-03-neotokyo/contracts/s1/NTCitizenDeploy.sol::592 => function functionStaticCall(address target, bytes memory data) internal view returns (bytes memory) {
2023-03-neotokyo/contracts/s1/NTCitizenDeploy.sol::619 => function functionDelegateCall(address target, bytes memory data) internal returns (bytes memory) {
2023-03-neotokyo/contracts/s1/NTItems.sol::510 => function functionCall(address target, bytes memory data) internal returns (bytes memory) {
2023-03-neotokyo/contracts/s1/NTItems.sol::572 => function functionStaticCall(address target, bytes memory data) internal view returns (bytes memory) {
2023-03-neotokyo/contracts/s1/NTItems.sol::599 => function functionDelegateCall(address target, bytes memory data) internal returns (bytes memory) {
2023-03-neotokyo/contracts/s1/NTItems.sol::1563 => function whitelistValidated(string memory vaultTokenId, uint256 leaderboardSpot,uint256 index, bytes32[] memory proof) internal view returns (bool) {
2023-03-neotokyo/contracts/s1/NTLandDeploy.sol::510 => function functionCall(address target, bytes memory data) internal returns (bytes memory) {
2023-03-neotokyo/contracts/s1/NTLandDeploy.sol::572 => function functionStaticCall(address target, bytes memory data) internal view returns (bytes memory) {
2023-03-neotokyo/contracts/s1/NTLandDeploy.sol::599 => function functionDelegateCall(address target, bytes memory data) internal returns (bytes memory) {
2023-03-neotokyo/contracts/s1/NTLandDeploy.sol::1400 => function whitelistValidated(string memory vaultTokenId, uint256 leaderboardSpot,uint256 index, bytes32[] memory proof) internal view returns (bool) {
2023-03-neotokyo/contracts/s1/beckLoot.sol::507 => function functionCall(address target, bytes memory data) internal returns (bytes memory) {
2023-03-neotokyo/contracts/s1/beckLoot.sol::569 => function functionStaticCall(address target, bytes memory data) internal view returns (bytes memory) {
2023-03-neotokyo/contracts/s1/beckLoot.sol::596 => function functionDelegateCall(address target, bytes memory data) internal returns (bytes memory) {
2023-03-neotokyo/contracts/s1/beckLoot.sol::1521 => function whitelistValidated(address wallet, uint256 index, bytes32[] memory proof) internal view returns (bool) {
2023-03-neotokyo/contracts/s1/vaultBox.sol::507 => function functionCall(address target, bytes memory data) internal returns (bytes memory) {
2023-03-neotokyo/contracts/s1/vaultBox.sol::569 => function functionStaticCall(address target, bytes memory data) internal view returns (bytes memory) {
2023-03-neotokyo/contracts/s1/vaultBox.sol::596 => function functionDelegateCall(address target, bytes memory data) internal returns (bytes memory) {
2023-03-neotokyo/contracts/s1/vaultBox.sol::1485 => function whitelistValidated(address wallet, uint256 index, bytes32[] memory proof) internal view returns (bool) {
2023-03-neotokyo/contracts/s2/NTOuterCitizenDeploy.sol::526 => function functionCall(address target, bytes memory data) internal returns (bytes memory) {
2023-03-neotokyo/contracts/s2/NTOuterCitizenDeploy.sol::588 => function functionStaticCall(address target, bytes memory data) internal view returns (bytes memory) {
2023-03-neotokyo/contracts/s2/NTOuterCitizenDeploy.sol::615 => function functionDelegateCall(address target, bytes memory data) internal returns (bytes memory) {
2023-03-neotokyo/contracts/s2/NTOuterIdentity.sol::511 => function functionCall(address target, bytes memory data) internal returns (bytes memory) {
2023-03-neotokyo/contracts/s2/NTOuterIdentity.sol::573 => function functionStaticCall(address target, bytes memory data) internal view returns (bytes memory) {
2023-03-neotokyo/contracts/s2/NTOuterIdentity.sol::600 => function functionDelegateCall(address target, bytes memory data) internal returns (bytes memory) {
2023-03-neotokyo/contracts/s2/NTOuterIdentity.sol::1532 => function whitelistValidated(address wallet, uint256 index, uint256 spotInLeaderboard, bytes32[] memory proof) internal view returns (bool) {
2023-03-neotokyo/contracts/s2/NTS2Items.sol::510 => function functionCall(address target, bytes memory data) internal returns (bytes memory) {
2023-03-neotokyo/contracts/s2/NTS2Items.sol::572 => function functionStaticCall(address target, bytes memory data) internal view returns (bytes memory) {
2023-03-neotokyo/contracts/s2/NTS2Items.sol::599 => function functionDelegateCall(address target, bytes memory data) internal returns (bytes memory) {
2023-03-neotokyo/contracts/s2/NTS2Items.sol::1380 => function whitelistValidated(uint256 identityTokenId, uint256 leaderboardSpot,uint256 index, bytes32[] memory proof) internal view returns (bool) {
2023-03-neotokyo/contracts/s2/NTS2LandDeploy.sol::510 => function functionCall(address target, bytes memory data) internal returns (bytes memory) {
2023-03-neotokyo/contracts/s2/NTS2LandDeploy.sol::572 => function functionStaticCall(address target, bytes memory data) internal view returns (bytes memory) {
2023-03-neotokyo/contracts/s2/NTS2LandDeploy.sol::599 => function functionDelegateCall(address target, bytes memory data) internal returns (bytes memory) {
2023-03-neotokyo/contracts/s2/NTS2LandDeploy.sol::1353 => function whitelistValidated(uint256 identityTokenId, uint256 leaderboardSpot,uint256 index, bytes32[] memory proof) internal view returns (bool) {
```

## [G-07] Using private rather than public for constants, saves gas

If needed, the value can be read from the verified contract source code. Savings are due to the compiler not having to create non-payable getter functions for deployment calldata, and not adding another entry to the method ID table

```
2023-03-neotokyo/contracts/staking/BYTES2.sol::43 => address immutable public BYTES1;
2023-03-neotokyo/contracts/staking/BYTES2.sol::46 => address immutable public S1_CITIZEN;
2023-03-neotokyo/contracts/staking/NeoTokyoStaker.sol::223 => address immutable public BYTES;
2023-03-neotokyo/contracts/staking/NeoTokyoStaker.sol::226 => address immutable public S1_CITIZEN;
2023-03-neotokyo/contracts/staking/NeoTokyoStaker.sol::229 => address immutable public S2_CITIZEN;
2023-03-neotokyo/contracts/staking/NeoTokyoStaker.sol::239 => address immutable public IDENTITY;
2023-03-neotokyo/contracts/staking/NeoTokyoStaker.sol::242 => address immutable public VAULT;
```

## [G-08] Functions guaranteed to revert when called by normal users can be marked payable

If a function modifier such as onlyOwner is used, the function will revert if a normal user tries to pay the function. Marking the function as payable will lower the gas cost for legitimate callers because the compiler will not include checks for whether a payment was provided. The extra opcodes avoided are CALLVALUE(2),DUP1(3),ISZERO(3),PUSH2(3),JUMPI(10),PUSH1(3),DUP1(3),REVERT(0),JUMPDEST(1),POP(2), which costs an average of about 21 gas per call to the function, in addition to the extra deployment cost

```
2023-03-neotokyo/contracts/s1/BYTESContract.sol::475 => function renounceOwnership() public virtual onlyOwner {
2023-03-neotokyo/contracts/s1/BYTESContract.sol::483 => function transferOwnership(address newOwner) public virtual onlyOwner {
2023-03-neotokyo/contracts/s1/BYTESContract.sol::864 => function updateMaxRewardableTokens(uint256 _amount) public onlyOwner
2023-03-neotokyo/contracts/s1/BYTESContract.sol::869 => function addAdminContractAddress(address _address) public onlyOwner
2023-03-neotokyo/contracts/s1/BYTESContract.sol::874 => function removeAdminContactAddress(address _address) public onlyOwner
2023-03-neotokyo/contracts/s1/BYTESContract.sol::900 => function setIdentityContract(address _address) public onlyOwner {
2023-03-neotokyo/contracts/s1/BYTESContract.sol::904 => function setBoughtIdentityContract(address _address) public onlyOwner {
2023-03-neotokyo/contracts/s1/BYTESContract.sol::908 => function setVaultBoxContract(address _address) public onlyOwner {
2023-03-neotokyo/contracts/s1/BYTESContract.sol::912 => function setCitizenContract(address _address) public onlyOwner {
2023-03-neotokyo/contracts/s1/NTCitizenDeploy.sol::328 => function renounceOwnership() public virtual onlyOwner {
2023-03-neotokyo/contracts/s1/NTCitizenDeploy.sol::336 => function transferOwnership(address newOwner) public virtual onlyOwner {
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
2023-03-neotokyo/contracts/s1/NTItems.sol::308 => function renounceOwnership() public virtual onlyOwner {
2023-03-neotokyo/contracts/s1/NTItems.sol::316 => function transferOwnership(address newOwner) public virtual onlyOwner {
2023-03-neotokyo/contracts/s1/NTItems.sol::1432 => function setItemMint() external onlyOwner
2023-03-neotokyo/contracts/s1/NTItems.sol::1548 => function emergencyClaim(uint256 boxId, uint256 tokenId) public onlyOwner {
2023-03-neotokyo/contracts/s1/NTItems.sol::1589 => function setItemMintActive() public onlyOwner {
2023-03-neotokyo/contracts/s1/NTItems.sol::1593 => function setItemCost(uint256 _cost) public onlyOwner {
2023-03-neotokyo/contracts/s1/NTItems.sol::1597 => function setContract(address contractAddress) public onlyOwner {
2023-03-neotokyo/contracts/s1/NTItems.sol::1601 => function setBoxAddress(address contractAddress) public onlyOwner {
2023-03-neotokyo/contracts/s1/NTItems.sol::1605 => function setBytesAddress(address contractAddress) public onlyOwner {
2023-03-neotokyo/contracts/s1/NTLandDeploy.sol::308 => function renounceOwnership() public virtual onlyOwner {
2023-03-neotokyo/contracts/s1/NTLandDeploy.sol::316 => function transferOwnership(address newOwner) public virtual onlyOwner {
2023-03-neotokyo/contracts/s1/NTLandDeploy.sol::1373 => function emergencyClaim(uint256 vaultId, uint256 tokenId) public onlyOwner {
2023-03-neotokyo/contracts/s1/NTLandDeploy.sol::1426 => function setLandMintActive() public onlyOwner {
2023-03-neotokyo/contracts/s1/NTLandDeploy.sol::1430 => function setLandCost(uint256 _cost) public onlyOwner {
2023-03-neotokyo/contracts/s1/NTLandDeploy.sol::1434 => function setContract(address contractAddress) public onlyOwner {
2023-03-neotokyo/contracts/s1/NTLandDeploy.sol::1438 => function setItemContract(address contractAddress) public onlyOwner {
2023-03-neotokyo/contracts/s1/NTLandDeploy.sol::1442 => function setVaultAddress(address contractAddress) public onlyOwner {
2023-03-neotokyo/contracts/s1/NTLandDeploy.sol::1446 => function setBytesAddress(address contractAddress) public onlyOwner {
2023-03-neotokyo/contracts/s1/beckLoot.sol::305 => function renounceOwnership() public virtual onlyOwner {
2023-03-neotokyo/contracts/s1/beckLoot.sol::313 => function transferOwnership(address newOwner) public virtual onlyOwner {
2023-03-neotokyo/contracts/s1/beckLoot.sol::1356 => function setSale() external onlyOwner
2023-03-neotokyo/contracts/s1/beckLoot.sol::1361 => function setWhitelistState() external onlyOwner
2023-03-neotokyo/contracts/s1/beckLoot.sol::1506 => function ownerClaim(uint256 tokenId) public nonReentrant onlyOwner {
2023-03-neotokyo/contracts/s1/beckLoot.sol::1511 => function riddleClaim(address _to, uint256 tokenId) public nonReentrant onlyOwner {
2023-03-neotokyo/contracts/s1/beckLoot.sol::1516 => function handClaim(address _to, uint256 tokenId) public nonReentrant onlyOwner {
2023-03-neotokyo/contracts/s1/beckLoot.sol::1548 => function setMintContract(address contractAddress) public onlyOwner {
2023-03-neotokyo/contracts/s1/beckLoot.sol::1552 => function setRareMintContract(address contractAddress) public onlyOwner {
2023-03-neotokyo/contracts/s1/beckLoot.sol::1556 => function setHandMintContract(address contractAddress) public onlyOwner {
2023-03-neotokyo/contracts/s1/vaultBox.sol::305 => function renounceOwnership() public virtual onlyOwner {
2023-03-neotokyo/contracts/s1/vaultBox.sol::313 => function transferOwnership(address newOwner) public virtual onlyOwner {
2023-03-neotokyo/contracts/s1/vaultBox.sol::1338 => function setSale() external onlyOwner
2023-03-neotokyo/contracts/s1/vaultBox.sol::1343 => function setWhitelistState() external onlyOwner
2023-03-neotokyo/contracts/s1/vaultBox.sol::1348 => function setOpenClaimState() external onlyOwner
2023-03-neotokyo/contracts/s1/vaultBox.sol::1459 => function ownerClaim(uint256 tokenId) public nonReentrant onlyOwner {
2023-03-neotokyo/contracts/s1/vaultBox.sol::1512 => function setContract(address contractAddress) public onlyOwner {
2023-03-neotokyo/contracts/s1/vaultBox.sol::1516 => function setIdAddress(address contractAddress) public onlyOwner {
2023-03-neotokyo/contracts/s2/NTOuterCitizenDeploy.sol::324 => function renounceOwnership() public virtual onlyOwner {
2023-03-neotokyo/contracts/s2/NTOuterCitizenDeploy.sol::332 => function transferOwnership(address newOwner) public virtual onlyOwner {
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
2023-03-neotokyo/contracts/s2/NTOuterIdentity.sol::309 => function renounceOwnership() public virtual onlyOwner {
2023-03-neotokyo/contracts/s2/NTOuterIdentity.sol::317 => function transferOwnership(address newOwner) public virtual onlyOwner {
2023-03-neotokyo/contracts/s2/NTOuterIdentity.sol::1283 => function setSaleActive() external onlyOwner
2023-03-neotokyo/contracts/s2/NTOuterIdentity.sol::1290 => function setWhitelistState() external onlyOwner
2023-03-neotokyo/contracts/s2/NTOuterIdentity.sol::1295 => function setBurnState() external onlyOwner
2023-03-neotokyo/contracts/s2/NTOuterIdentity.sol::1300 => function addRootHash(bytes32 _hash) external onlyOwner
2023-03-neotokyo/contracts/s2/NTOuterIdentity.sol::1305 => function setMaxMints(uint256 _value) external onlyOwner
2023-03-neotokyo/contracts/s2/NTOuterIdentity.sol::1310 => function setCitizenContract(address _address) external onlyOwner
2023-03-neotokyo/contracts/s2/NTOuterIdentity.sol::1315 => function setIdentityContract(address _address) external onlyOwner
2023-03-neotokyo/contracts/s2/NTOuterIdentity.sol::1325 => function startBurn() external onlyOwner
2023-03-neotokyo/contracts/s2/NTOuterIdentity.sol::1332 => function changeTimeToBurn(uint256 _time) external onlyOwner
2023-03-neotokyo/contracts/s2/NTOuterIdentity.sol::1526 => function ownerClaim(uint256 tokenId) public nonReentrant onlyOwner {
2023-03-neotokyo/contracts/s2/NTOuterIdentity.sol::1558 => function setOuterIdentityMintContract(address contractAddress) public onlyOwner {
2023-03-neotokyo/contracts/s2/NTOuterIdentity.sol::1562 => function setWithdrawalAddress(address _address) external onlyOwner
2023-03-neotokyo/contracts/s2/NTOuterIdentity.sol::1567 => function withdraw() external onlyOwner
2023-03-neotokyo/contracts/s2/NTS2Items.sol::308 => function renounceOwnership() public virtual onlyOwner {
2023-03-neotokyo/contracts/s2/NTS2Items.sol::316 => function transferOwnership(address newOwner) public virtual onlyOwner {
2023-03-neotokyo/contracts/s2/NTS2Items.sol::1269 => function setItemMint() external onlyOwner
2023-03-neotokyo/contracts/s2/NTS2Items.sol::1324 => function addRootHash(bytes32 _hash) external onlyOwner {
2023-03-neotokyo/contracts/s2/NTS2Items.sol::1365 => function emergencyClaim(uint256 identityId, uint256 tokenId) public onlyOwner {
2023-03-neotokyo/contracts/s2/NTS2Items.sol::1406 => function setItemMintActive() public onlyOwner {
2023-03-neotokyo/contracts/s2/NTS2Items.sol::1410 => function setItemCost(uint256 _cost) public onlyOwner {
2023-03-neotokyo/contracts/s2/NTS2Items.sol::1414 => function setContract(address contractAddress) public onlyOwner {
2023-03-neotokyo/contracts/s2/NTS2Items.sol::1418 => function setIdentityAddress(address contractAddress) public onlyOwner {
2023-03-neotokyo/contracts/s2/NTS2Items.sol::1422 => function setBytesAddress(address contractAddress) public onlyOwner {
2023-03-neotokyo/contracts/s2/NTS2LandDeploy.sol::308 => function renounceOwnership() public virtual onlyOwner {
2023-03-neotokyo/contracts/s2/NTS2LandDeploy.sol::316 => function transferOwnership(address newOwner) public virtual onlyOwner {
2023-03-neotokyo/contracts/s2/NTS2LandDeploy.sol::1338 => function emergencyClaim(uint256 identityId, uint256 tokenId) public onlyOwner {
2023-03-neotokyo/contracts/s2/NTS2LandDeploy.sol::1379 => function setLandMintActive() public onlyOwner {
2023-03-neotokyo/contracts/s2/NTS2LandDeploy.sol::1383 => function setLandCost(uint256 _cost) public onlyOwner {
2023-03-neotokyo/contracts/s2/NTS2LandDeploy.sol::1387 => function setContract(address contractAddress) public onlyOwner {
2023-03-neotokyo/contracts/s2/NTS2LandDeploy.sol::1391 => function setIdentityContract(address contractAddress) public onlyOwner {
2023-03-neotokyo/contracts/s2/NTS2LandDeploy.sol::1395 => function setBytesAddress(address contractAddress) public onlyOwner {
```

## [G-09] Usage of uints/ints smaller than 32 bytes (256 bits) incurs overhead

When using elements that are smaller than 32 bytes, your contract’s gas usage may be higher. This is because the EVM operates on 32 bytes at a time. Therefore, if the element is smaller than that, the EVM must use more operations in order to reduce the size of the element from 32 bytes to the desired size.

```
2023-03-neotokyo/contracts/s1/BYTESContract.sol::533 => uint8 private _decimals;
2023-03-neotokyo/contracts/s1/vaultBox.sol::1468 => uint8 i;
2023-03-neotokyo/contracts/staking/NeoTokyoStaker.sol::291 => uint128 startTime;
2023-03-neotokyo/contracts/staking/NeoTokyoStaker.sol::292 => uint128 reward;
2023-03-neotokyo/contracts/staking/NeoTokyoStaker.sol::963 => uint256 timelockMultiplier = _timelock & type(uint128).max;
2023-03-neotokyo/contracts/staking/NeoTokyoStaker.sol::1017 => uint256 timelockMultiplier = _timelock & type(uint128).max;
2023-03-neotokyo/contracts/staking/NeoTokyoStaker.sol::1141 => uint256 timelockMultiplier = _timelock & type(uint128).max;
```

## [G-10] Using bools for storage incurs overhead

Booleans are more expensive than uint256 or any type that takes up a full word because each write operation emits an extra SLOAD to first read the slot's contents, replace the bits taken up by the boolean, and then write back. This is the compiler's defense against contract upgrades and pointer aliasing, and it cannot be disabled.
Use uint256(1) and uint256(2) for true/false instead

```
2023-03-neotokyo/contracts/s1/BYTESContract.sol::805 => mapping(address => bool) public adminContracts;
2023-03-neotokyo/contracts/s1/NTCitizenDeploy.sol::715 => mapping(address => mapping(address => bool)) private _operatorApprovals;
2023-03-neotokyo/contracts/s1/NTCitizenDeploy.sol::1308 => mapping(uint256 => bool ) private _genderFemale;
2023-03-neotokyo/contracts/s1/NTItems.sol::695 => mapping(address => mapping(address => bool)) private _operatorApprovals;
2023-03-neotokyo/contracts/s1/NTLandDeploy.sol::695 => mapping(address => mapping(address => bool)) private _operatorApprovals;
2023-03-neotokyo/contracts/s1/beckLoot.sol::692 => mapping(address => mapping(address => bool)) private _operatorApprovals;
2023-03-neotokyo/contracts/s1/vaultBox.sol::692 => mapping(address => mapping(address => bool)) private _operatorApprovals;
2023-03-neotokyo/contracts/s2/NTOuterCitizenDeploy.sol::711 => mapping(address => mapping(address => bool)) private _operatorApprovals;
2023-03-neotokyo/contracts/s2/NTOuterCitizenDeploy.sol::1305 => mapping(uint256 => bool ) private _genderFemale;
2023-03-neotokyo/contracts/s2/NTOuterIdentity.sol::696 => mapping(address => mapping(address => bool)) private _operatorApprovals;
2023-03-neotokyo/contracts/s2/NTOuterIdentity.sol::1265 => bool public saleActive;
2023-03-neotokyo/contracts/s2/NTOuterIdentity.sol::1266 => bool public whitelistActive;
2023-03-neotokyo/contracts/s2/NTOuterIdentity.sol::1267 => bool public burnActive;
2023-03-neotokyo/contracts/s2/NTOuterIdentity.sol::1281 => mapping(address => bool) public addressHasReserved;
2023-03-neotokyo/contracts/s2/NTS2Items.sol::695 => mapping(address => mapping(address => bool)) private _operatorApprovals;
2023-03-neotokyo/contracts/s2/NTS2LandDeploy.sol::695 => mapping(address => mapping(address => bool)) private _operatorApprovals;
2023-03-neotokyo/contracts/staking/NeoTokyoStaker.sol::511 => bool public lpLocked;
```

## [G-11] ++i/i++ should be unchecked{++i}/unchecked{i++} when it is not possible for them to overflow, for example when used in for- and while-loops

The unchecked keyword is new in solidity version 0.8.0, so this only applies to that version or higher, which these instances are. This saves 30-40 gas per loop

```
2023-03-neotokyo/contracts/s1/BYTESContract.sol::1000 => for (uint256 i = 0; i < bytes(_str).length; i++) {
2023-03-neotokyo/contracts/s1/NTCitizenDeploy.sol::215 => digits++;
2023-03-neotokyo/contracts/s1/NTCitizenDeploy.sol::238 => length++;
2023-03-neotokyo/contracts/s1/NTCitizenDeploy.sol::1363 => for(uint256 index = 0; index < ERC721.balanceOf(_user); index++)
2023-03-neotokyo/contracts/s1/NTCitizenDeploy.sol::1428 => newestCitizen++;
2023-03-neotokyo/contracts/s1/NTCitizenDeploy.sol::1649 => digits++;
2023-03-neotokyo/contracts/s1/NTItems.sol::195 => digits++;
2023-03-neotokyo/contracts/s1/NTItems.sol::218 => length++;
2023-03-neotokyo/contracts/s1/NTItems.sol::1532 => specialItemCount++;
2023-03-neotokyo/contracts/s1/NTItems.sol::1544 => boughtItemsCount++;
2023-03-neotokyo/contracts/s1/NTItems.sol::1568 => for (uint16 i = 0; i < proof.length; i++) {
2023-03-neotokyo/contracts/s1/NTItems.sol::1578 => for(uint i = 0; i < _rootHash.length; i++)
2023-03-neotokyo/contracts/s1/NTItems.sol::1620 => digits++;
2023-03-neotokyo/contracts/s1/NTLandDeploy.sol::195 => digits++;
2023-03-neotokyo/contracts/s1/NTLandDeploy.sol::218 => length++;
2023-03-neotokyo/contracts/s1/NTLandDeploy.sol::1369 => boughtLandCount++;
2023-03-neotokyo/contracts/s1/NTLandDeploy.sol::1405 => for (uint16 i = 0; i < proof.length; i++) {
2023-03-neotokyo/contracts/s1/NTLandDeploy.sol::1415 => for(uint i = 0; i < _rootHash.length; i++)
2023-03-neotokyo/contracts/s1/NTLandDeploy.sol::1461 => digits++;
2023-03-neotokyo/contracts/s1/beckLoot.sol::192 => digits++;
2023-03-neotokyo/contracts/s1/beckLoot.sol::215 => length++;
2023-03-neotokyo/contracts/s1/beckLoot.sol::1527 => for (uint16 i = 0; i < proof.length; i++) {
2023-03-neotokyo/contracts/s1/beckLoot.sol::1537 => for(uint i = 0; i < _rootHash.length; i++)
2023-03-neotokyo/contracts/s1/beckLoot.sol::1590 => digits++;
2023-03-neotokyo/contracts/s1/vaultBox.sol::192 => digits++;
2023-03-neotokyo/contracts/s1/vaultBox.sol::215 => length++;
2023-03-neotokyo/contracts/s1/vaultBox.sol::1478 => i++;
2023-03-neotokyo/contracts/s1/vaultBox.sol::1491 => for (uint16 i = 0; i < proof.length; i++) {
2023-03-neotokyo/contracts/s1/vaultBox.sol::1501 => for(uint i = 0; i < _rootHash.length; i++)
2023-03-neotokyo/contracts/s1/vaultBox.sol::1531 => digits++;
2023-03-neotokyo/contracts/s2/NTOuterCitizenDeploy.sol::211 => digits++;
2023-03-neotokyo/contracts/s2/NTOuterCitizenDeploy.sol::234 => length++;
2023-03-neotokyo/contracts/s2/NTOuterCitizenDeploy.sol::1365 => newestCitizen++;
2023-03-neotokyo/contracts/s2/NTOuterCitizenDeploy.sol::1549 => digits++;
2023-03-neotokyo/contracts/s2/NTOuterIdentity.sol::196 => digits++;
2023-03-neotokyo/contracts/s2/NTOuterIdentity.sol::219 => length++;
2023-03-neotokyo/contracts/s2/NTOuterIdentity.sol::1484 => totalMembersInBytesAuction++;
2023-03-neotokyo/contracts/s2/NTOuterIdentity.sol::1537 => for (uint16 i = 0; i < proof.length; i++) {
2023-03-neotokyo/contracts/s2/NTOuterIdentity.sol::1547 => for(uint i = 0; i < _rootHash.length; i++)
2023-03-neotokyo/contracts/s2/NTOuterIdentity.sol::1583 => digits++;
2023-03-neotokyo/contracts/s2/NTS2Items.sol::195 => digits++;
2023-03-neotokyo/contracts/s2/NTS2Items.sol::218 => length++;
2023-03-neotokyo/contracts/s2/NTS2Items.sol::1361 => boughtItemsCount++;
2023-03-neotokyo/contracts/s2/NTS2Items.sol::1385 => for (uint16 i = 0; i < proof.length; i++) {
2023-03-neotokyo/contracts/s2/NTS2Items.sol::1395 => for(uint i = 0; i < _rootHash.length; i++)
2023-03-neotokyo/contracts/s2/NTS2Items.sol::1437 => digits++;
2023-03-neotokyo/contracts/s2/NTS2LandDeploy.sol::195 => digits++;
2023-03-neotokyo/contracts/s2/NTS2LandDeploy.sol::218 => length++;
2023-03-neotokyo/contracts/s2/NTS2LandDeploy.sol::1334 => boughtLandCount++;
2023-03-neotokyo/contracts/s2/NTS2LandDeploy.sol::1358 => for (uint16 i = 0; i < proof.length; i++) {
2023-03-neotokyo/contracts/s2/NTS2LandDeploy.sol::1368 => for(uint i = 0; i < _rootHash.length; i++)
2023-03-neotokyo/contracts/s2/NTS2LandDeploy.sol::1410 => digits++;
2023-03-neotokyo/contracts/staking/NeoTokyoStaker.sol::1284 => i++;
2023-03-neotokyo/contracts/staking/NeoTokyoStaker.sol::1293 => i++;
```

## [G-12] <x> += <y> costs more gas than <x> = <x> + <y> for state variables

use <x> = <x> + <y> or <x> = <x> - <y> instead to save gas

```
2023-03-neotokyo/contracts/s1/BYTESContract.sol::953 => rewards[_from] += citizenContract.getRewardRate(_from).mul((time.sub(timerFrom))).div(86400);
2023-03-neotokyo/contracts/s1/BYTESContract.sol::963 => rewards[_to] += citizenContract.getRewardRate(_to).mul((time.sub(timerTo))).div(86400);
2023-03-neotokyo/contracts/s1/BYTESContract.sol::1004 => res += (uint8(bytes(_str)[i]) - 48) * 10**(bytes(_str).length - i - 1);
2023-03-neotokyo/contracts/s1/NTCitizenDeploy.sol::220 => digits -= 1;
2023-03-neotokyo/contracts/s1/NTCitizenDeploy.sol::965 => _balances[to] += 1;
2023-03-neotokyo/contracts/s1/NTCitizenDeploy.sol::989 => _balances[owner] -= 1;
2023-03-neotokyo/contracts/s1/NTCitizenDeploy.sol::1019 => _balances[from] -= 1;
2023-03-neotokyo/contracts/s1/NTCitizenDeploy.sol::1020 => _balances[to] += 1;
2023-03-neotokyo/contracts/s1/NTCitizenDeploy.sol::1654 => digits -= 1;
2023-03-neotokyo/contracts/s1/NTItems.sol::200 => digits -= 1;
2023-03-neotokyo/contracts/s1/NTItems.sol::945 => _balances[to] += 1;
2023-03-neotokyo/contracts/s1/NTItems.sol::969 => _balances[owner] -= 1;
2023-03-neotokyo/contracts/s1/NTItems.sol::999 => _balances[from] -= 1;
2023-03-neotokyo/contracts/s1/NTItems.sol::1000 => _balances[to] += 1;
2023-03-neotokyo/contracts/s1/NTItems.sol::1625 => digits -= 1;
2023-03-neotokyo/contracts/s1/NTLandDeploy.sol::200 => digits -= 1;
2023-03-neotokyo/contracts/s1/NTLandDeploy.sol::945 => _balances[to] += 1;
2023-03-neotokyo/contracts/s1/NTLandDeploy.sol::969 => _balances[owner] -= 1;
2023-03-neotokyo/contracts/s1/NTLandDeploy.sol::999 => _balances[from] -= 1;
2023-03-neotokyo/contracts/s1/NTLandDeploy.sol::1000 => _balances[to] += 1;
2023-03-neotokyo/contracts/s1/NTLandDeploy.sol::1466 => digits -= 1;
2023-03-neotokyo/contracts/s1/beckLoot.sol::197 => digits -= 1;
2023-03-neotokyo/contracts/s1/beckLoot.sol::942 => _balances[to] += 1;
2023-03-neotokyo/contracts/s1/beckLoot.sol::966 => _balances[owner] -= 1;
2023-03-neotokyo/contracts/s1/beckLoot.sol::996 => _balances[from] -= 1;
2023-03-neotokyo/contracts/s1/beckLoot.sol::997 => _balances[to] += 1;
2023-03-neotokyo/contracts/s1/beckLoot.sol::1595 => digits -= 1;
2023-03-neotokyo/contracts/s1/vaultBox.sol::197 => digits -= 1;
2023-03-neotokyo/contracts/s1/vaultBox.sol::942 => _balances[to] += 1;
2023-03-neotokyo/contracts/s1/vaultBox.sol::966 => _balances[owner] -= 1;
2023-03-neotokyo/contracts/s1/vaultBox.sol::996 => _balances[from] -= 1;
2023-03-neotokyo/contracts/s1/vaultBox.sol::997 => _balances[to] += 1;
2023-03-neotokyo/contracts/s1/vaultBox.sol::1536 => digits -= 1;
2023-03-neotokyo/contracts/s2/NTOuterCitizenDeploy.sol::216 => digits -= 1;
2023-03-neotokyo/contracts/s2/NTOuterCitizenDeploy.sol::961 => _balances[to] += 1;
2023-03-neotokyo/contracts/s2/NTOuterCitizenDeploy.sol::985 => _balances[owner] -= 1;
2023-03-neotokyo/contracts/s2/NTOuterCitizenDeploy.sol::1015 => _balances[from] -= 1;
2023-03-neotokyo/contracts/s2/NTOuterCitizenDeploy.sol::1016 => _balances[to] += 1;
2023-03-neotokyo/contracts/s2/NTOuterCitizenDeploy.sol::1554 => digits -= 1;
2023-03-neotokyo/contracts/s2/NTOuterIdentity.sol::201 => digits -= 1;
2023-03-neotokyo/contracts/s2/NTOuterIdentity.sol::946 => _balances[to] += 1;
2023-03-neotokyo/contracts/s2/NTOuterIdentity.sol::970 => _balances[owner] -= 1;
2023-03-neotokyo/contracts/s2/NTOuterIdentity.sol::1000 => _balances[from] -= 1;
2023-03-neotokyo/contracts/s2/NTOuterIdentity.sol::1001 => _balances[to] += 1;
2023-03-neotokyo/contracts/s2/NTOuterIdentity.sol::1588 => digits -= 1;
2023-03-neotokyo/contracts/s2/NTS2Items.sol::200 => digits -= 1;
2023-03-neotokyo/contracts/s2/NTS2Items.sol::945 => _balances[to] += 1;
2023-03-neotokyo/contracts/s2/NTS2Items.sol::969 => _balances[owner] -= 1;
2023-03-neotokyo/contracts/s2/NTS2Items.sol::999 => _balances[from] -= 1;
2023-03-neotokyo/contracts/s2/NTS2Items.sol::1000 => _balances[to] += 1;
2023-03-neotokyo/contracts/s2/NTS2Items.sol::1442 => digits -= 1;
2023-03-neotokyo/contracts/s2/NTS2LandDeploy.sol::200 => digits -= 1;
2023-03-neotokyo/contracts/s2/NTS2LandDeploy.sol::945 => _balances[to] += 1;
2023-03-neotokyo/contracts/s2/NTS2LandDeploy.sol::969 => _balances[owner] -= 1;
2023-03-neotokyo/contracts/s2/NTS2LandDeploy.sol::999 => _balances[from] -= 1;
2023-03-neotokyo/contracts/s2/NTS2LandDeploy.sol::1000 => _balances[to] += 1;
2023-03-neotokyo/contracts/s2/NTS2LandDeploy.sol::1415 => digits -= 1;
2023-03-neotokyo/contracts/staking/NeoTokyoStaker.sol::977 => pool.totalPoints += citizenStatus.points;
2023-03-neotokyo/contracts/staking/NeoTokyoStaker.sol::1029 => pool.totalPoints += citizenStatus.points;
2023-03-neotokyo/contracts/staking/NeoTokyoStaker.sol::1078 => citizenStatus.stakedBytes += amount;
2023-03-neotokyo/contracts/staking/NeoTokyoStaker.sol::1079 => citizenStatus.points += bonusPoints;
2023-03-neotokyo/contracts/staking/NeoTokyoStaker.sol::1080 => pool.totalPoints += bonusPoints;
2023-03-neotokyo/contracts/staking/NeoTokyoStaker.sol::1099 => citizenStatus.stakedBytes += amount;
2023-03-neotokyo/contracts/staking/NeoTokyoStaker.sol::1100 => citizenStatus.points += bonusPoints;
2023-03-neotokyo/contracts/staking/NeoTokyoStaker.sol::1101 => pool.totalPoints += bonusPoints;
2023-03-neotokyo/contracts/staking/NeoTokyoStaker.sol::1160 => stakerLPPosition[msg.sender].amount += amount;
2023-03-neotokyo/contracts/staking/NeoTokyoStaker.sol::1161 => stakerLPPosition[msg.sender].points += points;
2023-03-neotokyo/contracts/staking/NeoTokyoStaker.sol::1164 => pool.totalPoints += points;
2023-03-neotokyo/contracts/staking/NeoTokyoStaker.sol::1283 => points += s1Citizen.points;
2023-03-neotokyo/contracts/staking/NeoTokyoStaker.sol::1292 => points += s2Citizen.points;
2023-03-neotokyo/contracts/staking/NeoTokyoStaker.sol::1298 => points += stakerLPPosition[_recipient].points;
2023-03-neotokyo/contracts/staking/NeoTokyoStaker.sol::1332 => totalReward += currentRewardRate * timeSinceReward;
2023-03-neotokyo/contracts/staking/NeoTokyoStaker.sol::1343 => totalReward += currentRewardRate * timeSinceReward;
2023-03-neotokyo/contracts/staking/NeoTokyoStaker.sol::1357 => totalReward += currentRewardRate * timeSinceReward;
2023-03-neotokyo/contracts/staking/NeoTokyoStaker.sol::1515 => pool.totalPoints -= stakedCitizen.points;
2023-03-neotokyo/contracts/staking/NeoTokyoStaker.sol::1580 => pool.totalPoints -= stakedCitizen.points;
2023-03-neotokyo/contracts/staking/NeoTokyoStaker.sol::1626 => lpPosition.amount -= amount;
2023-03-neotokyo/contracts/staking/NeoTokyoStaker.sol::1627 => lpPosition.points -= points;
2023-03-neotokyo/contracts/staking/NeoTokyoStaker.sol::1630 => pool.totalPoints -= points;
```

## [G-13] Use custom errors rather than revert()/require() strings to save gas

Custom errors are available from solidity version 0.8.4. Custom errors save ~50 gas each time they're hitby avoiding having to allocate and store the revert string. Not defining the strings also save deployment gas

```
2023-03-neotokyo/contracts/access/PermitControl.sol::128 => require(_managedRight != ZERO_RIGHT, "P3");
2023-03-neotokyo/contracts/access/PermitControl.sol::150 => require(_right != ZERO_RIGHT, "P2");
2023-03-neotokyo/contracts/s1/BYTESContract.sol::83 => require(address(this).balance >= amount, "Address: insufficient balance");
2023-03-neotokyo/contracts/s1/BYTESContract.sol::87 => require(success, "Address: unable to send value, recipient may have reverted");
2023-03-neotokyo/contracts/s1/BYTESContract.sol::144 => require(address(this).balance >= value, "Address: insufficient balance for call");
2023-03-neotokyo/contracts/s1/BYTESContract.sol::149 => require(isContract(target), "Address: call to non-contract");
2023-03-neotokyo/contracts/s1/BYTESContract.sol::299 => require(c >= a, "SafeMath: addition overflow");
2023-03-neotokyo/contracts/s1/BYTESContract.sol::354 => require(c / a == b, "SafeMath: multiplication overflow");
2023-03-neotokyo/contracts/s1/BYTESContract.sol::464 => require(owner() == _msgSender(), "Ownable: caller is not the owner");
2023-03-neotokyo/contracts/s1/BYTESContract.sol::484 => require(newOwner != address(0), "Ownable: new owner is the zero address");
2023-03-neotokyo/contracts/s1/BYTESContract.sol::697 => require(sender != address(0), "ERC20: transfer from the zero address");
2023-03-neotokyo/contracts/s1/BYTESContract.sol::698 => require(recipient != address(0), "ERC20: transfer to the zero address");
2023-03-neotokyo/contracts/s1/BYTESContract.sol::717 => require(account != address(0), "ERC20: mint to the zero address");
2023-03-neotokyo/contracts/s1/BYTESContract.sol::738 => require(account != address(0), "ERC20: burn from the zero address");
2023-03-neotokyo/contracts/s1/BYTESContract.sol::761 => require(owner != address(0), "ERC20: approve from the zero address");
2023-03-neotokyo/contracts/s1/BYTESContract.sol::762 => require(spender != address(0), "ERC20: approve to the zero address");
2023-03-neotokyo/contracts/s1/BYTESContract.sol::825 => require(identityBoxOpened[identityTokenId] == 0, "That identity has already opened a box");
2023-03-neotokyo/contracts/s1/BYTESContract.sol::826 => require(vaultBoxOpenedByIdentity[vaultBoxTokenId] == 0, "That box has already been opened");
2023-03-neotokyo/contracts/s1/BYTESContract.sol::827 => require(validateIdentity(identityTokenId), "You don't own that identity");
2023-03-neotokyo/contracts/s1/BYTESContract.sol::828 => require(vaultBoxContract.ownerOf(vaultBoxTokenId) == msg.sender, "You don't own that vault box");
2023-03-neotokyo/contracts/s1/BYTESContract.sol::919 => require(msg.sender == address(citizenContract), "Can't call this");
2023-03-neotokyo/contracts/s1/BYTESContract.sol::987 => require(adminContracts[msg.sender], "You are not approved to burn tokens");
2023-03-neotokyo/contracts/s1/NTCitizenDeploy.sol::255 => require(value == 0, "Strings: hex length insufficient");
2023-03-neotokyo/contracts/s1/NTCitizenDeploy.sol::317 => require(owner() == _msgSender(), "Ownable: caller is not the owner");
2023-03-neotokyo/contracts/s1/NTCitizenDeploy.sol::337 => require(newOwner != address(0), "Ownable: new owner is the zero address");
2023-03-neotokyo/contracts/s1/NTCitizenDeploy.sol::395 => require(_status != _ENTERED, "ReentrancyGuard: reentrant call");
2023-03-neotokyo/contracts/s1/NTCitizenDeploy.sol::506 => require(address(this).balance >= amount, "Address: insufficient balance");
2023-03-neotokyo/contracts/s1/NTCitizenDeploy.sol::509 => require(success, "Address: unable to send value, recipient may have reverted");
2023-03-neotokyo/contracts/s1/NTCitizenDeploy.sol::579 => require(address(this).balance >= value, "Address: insufficient balance for call");
2023-03-neotokyo/contracts/s1/NTCitizenDeploy.sol::580 => require(isContract(target), "Address: call to non-contract");
2023-03-neotokyo/contracts/s1/NTCitizenDeploy.sol::607 => require(isContract(target), "Address: static call to non-contract");
2023-03-neotokyo/contracts/s1/NTCitizenDeploy.sol::634 => require(isContract(target), "Address: delegate call to non-contract");
2023-03-neotokyo/contracts/s1/NTCitizenDeploy.sol::739 => require(owner != address(0), "ERC721: balance query for the zero address");
2023-03-neotokyo/contracts/s1/NTCitizenDeploy.sol::748 => require(owner != address(0), "ERC721: owner query for nonexistent token");
2023-03-neotokyo/contracts/s1/NTCitizenDeploy.sol::770 => require(_exists(tokenId), "ERC721Metadata: URI query for nonexistent token");
2023-03-neotokyo/contracts/s1/NTCitizenDeploy.sol::790 => require(to != owner, "ERC721: approval to current owner");
2023-03-neotokyo/contracts/s1/NTCitizenDeploy.sol::804 => require(_exists(tokenId), "ERC721: approved query for nonexistent token");
2023-03-neotokyo/contracts/s1/NTCitizenDeploy.sol::813 => require(operator != _msgSender(), "ERC721: approve to caller");
2023-03-neotokyo/contracts/s1/NTCitizenDeploy.sol::835 => require(_isApprovedOrOwner(_msgSender(), tokenId), "ERC721: transfer caller is not owner nor approved");
2023-03-neotokyo/contracts/s1/NTCitizenDeploy.sol::860 => require(_isApprovedOrOwner(_msgSender(), tokenId), "ERC721: transfer caller is not owner nor approved");
2023-03-neotokyo/contracts/s1/NTCitizenDeploy.sol::889 => require(_checkOnERC721Received(from, to, tokenId, _data), "ERC721: transfer to non ERC721Receiver implementer");
2023-03-neotokyo/contracts/s1/NTCitizenDeploy.sol::912 => require(_exists(tokenId), "ERC721: operator query for nonexistent token");
2023-03-neotokyo/contracts/s1/NTCitizenDeploy.sol::960 => require(to != address(0), "ERC721: mint to the zero address");
2023-03-neotokyo/contracts/s1/NTCitizenDeploy.sol::961 => require(!_exists(tokenId), "ERC721: token already minted");
2023-03-neotokyo/contracts/s1/NTCitizenDeploy.sol::1011 => require(ERC721.ownerOf(tokenId) == from, "ERC721: transfer of token that is not own");
2023-03-neotokyo/contracts/s1/NTCitizenDeploy.sol::1012 => require(to != address(0), "ERC721: transfer to the zero address");
2023-03-neotokyo/contracts/s1/NTCitizenDeploy.sol::1145 => require(index < ERC721.balanceOf(owner), "ERC721Enumerable: owner index out of bounds");
2023-03-neotokyo/contracts/s1/NTCitizenDeploy.sol::1160 => require(index < ERC721Enumerable.totalSupply(), "ERC721Enumerable: global index out of bounds");
2023-03-neotokyo/contracts/s1/NTCitizenDeploy.sol::1342 => require(_exists(tokenId), "ERC721Metadata: URI query for nonexistent token");
2023-03-neotokyo/contracts/s1/NTCitizenDeploy.sol::1371 => require(_exists(tokenId) || tokenId == 0, "Citizen does not exist");
2023-03-neotokyo/contracts/s1/NTCitizenDeploy.sol::1376 => require(_exists(tokenId) || tokenId == 0, "Citizen does not exist");
2023-03-neotokyo/contracts/s1/NTCitizenDeploy.sol::1411 => require(citizenMintActive, "Uploading is not currently active");
2023-03-neotokyo/contracts/s1/NTCitizenDeploy.sol::1412 => require(identityValidated(identityId), "You are not the owner of that identity");
2023-03-neotokyo/contracts/s1/NTCitizenDeploy.sol::1413 => require(itemCacheValidated(itemCacheId), "You are not the owner of that item cache");
2023-03-neotokyo/contracts/s1/NTCitizenDeploy.sol::1414 => require(landDeedValidated(landDeedId), "You are not the owner of that land deed");
2023-03-neotokyo/contracts/s1/NTCitizenDeploy.sol::1418 => require(femaleActive, "Females cannot be uploaded yet");
2023-03-neotokyo/contracts/s1/NTCitizenDeploy.sol::1423 => require(vaultValidated(vaultId), "You are not the owner of that vault box");
2023-03-neotokyo/contracts/s1/NTCitizenDeploy.sol::1477 => require(ownerOf(citizenId) == _msgSender(), "You do not own that citizen");
2023-03-neotokyo/contracts/s1/NTCitizenDeploy.sol::1515 => require(femaleActive, "Females cannot be uploaded yet");
2023-03-neotokyo/contracts/s1/NTCitizenDeploy.sol::1516 => require(ownerOf(tokenId) == _msgSender(), "You do not own that citizen");
2023-03-neotokyo/contracts/s1/NTCitizenDeploy.sol::1524 => require(ownerOf(tokenId) == _msgSender(), "You do not own that citizen");
2023-03-neotokyo/contracts/s1/NTCitizenDeploy.sol::1613 => require(boughtIdentitiesActive, "Identities cannot be bought yet");
2023-03-neotokyo/contracts/s1/NTCitizenDeploy.sol::1614 => require(address(boughtIdentityContract) != address(0), "Identity contract not set");
2023-03-neotokyo/contracts/s1/NTItems.sol::235 => require(value == 0, "Strings: hex length insufficient");
2023-03-neotokyo/contracts/s1/NTItems.sol::297 => require(owner() == _msgSender(), "Ownable: caller is not the owner");
2023-03-neotokyo/contracts/s1/NTItems.sol::317 => require(newOwner != address(0), "Ownable: new owner is the zero address");
2023-03-neotokyo/contracts/s1/NTItems.sol::375 => require(_status != _ENTERED, "ReentrancyGuard: reentrant call");
2023-03-neotokyo/contracts/s1/NTItems.sol::486 => require(address(this).balance >= amount, "Address: insufficient balance");
2023-03-neotokyo/contracts/s1/NTItems.sol::489 => require(success, "Address: unable to send value, recipient may have reverted");
2023-03-neotokyo/contracts/s1/NTItems.sol::559 => require(address(this).balance >= value, "Address: insufficient balance for call");
2023-03-neotokyo/contracts/s1/NTItems.sol::560 => require(isContract(target), "Address: call to non-contract");
2023-03-neotokyo/contracts/s1/NTItems.sol::587 => require(isContract(target), "Address: static call to non-contract");
2023-03-neotokyo/contracts/s1/NTItems.sol::614 => require(isContract(target), "Address: delegate call to non-contract");
2023-03-neotokyo/contracts/s1/NTItems.sol::719 => require(owner != address(0), "ERC721: balance query for the zero address");
2023-03-neotokyo/contracts/s1/NTItems.sol::728 => require(owner != address(0), "ERC721: owner query for nonexistent token");
2023-03-neotokyo/contracts/s1/NTItems.sol::750 => require(_exists(tokenId), "ERC721Metadata: URI query for nonexistent token");
2023-03-neotokyo/contracts/s1/NTItems.sol::770 => require(to != owner, "ERC721: approval to current owner");
2023-03-neotokyo/contracts/s1/NTItems.sol::784 => require(_exists(tokenId), "ERC721: approved query for nonexistent token");
2023-03-neotokyo/contracts/s1/NTItems.sol::793 => require(operator != _msgSender(), "ERC721: approve to caller");
2023-03-neotokyo/contracts/s1/NTItems.sol::815 => require(_isApprovedOrOwner(_msgSender(), tokenId), "ERC721: transfer caller is not owner nor approved");
2023-03-neotokyo/contracts/s1/NTItems.sol::840 => require(_isApprovedOrOwner(_msgSender(), tokenId), "ERC721: transfer caller is not owner nor approved");
2023-03-neotokyo/contracts/s1/NTItems.sol::869 => require(_checkOnERC721Received(from, to, tokenId, _data), "ERC721: transfer to non ERC721Receiver implementer");
2023-03-neotokyo/contracts/s1/NTItems.sol::892 => require(_exists(tokenId), "ERC721: operator query for nonexistent token");
2023-03-neotokyo/contracts/s1/NTItems.sol::940 => require(to != address(0), "ERC721: mint to the zero address");
2023-03-neotokyo/contracts/s1/NTItems.sol::941 => require(!_exists(tokenId), "ERC721: token already minted");
2023-03-neotokyo/contracts/s1/NTItems.sol::991 => require(ERC721.ownerOf(tokenId) == from, "ERC721: transfer of token that is not own");
2023-03-neotokyo/contracts/s1/NTItems.sol::992 => require(to != address(0), "ERC721: transfer to the zero address");
2023-03-neotokyo/contracts/s1/NTItems.sol::1125 => require(index < ERC721.balanceOf(owner), "ERC721Enumerable: owner index out of bounds");
2023-03-neotokyo/contracts/s1/NTItems.sol::1140 => require(index < ERC721Enumerable.totalSupply(), "ERC721Enumerable: global index out of bounds");
2023-03-neotokyo/contracts/s1/NTItems.sol::1444 => require(_exists(tokenId), "ERC721Metadata: URI query for nonexistent token");
2023-03-neotokyo/contracts/s1/NTItems.sol::1455 => require(_exists(tokenId), "ERC721Metadata: URI query for nonexistent token");
2023-03-neotokyo/contracts/s1/NTItems.sol::1465 => require(_exists(tokenId), "ERC721Metadata: URI query for nonexistent token");
2023-03-neotokyo/contracts/s1/NTItems.sol::1475 => require(_exists(tokenId), "ERC721Metadata: URI query for nonexistent token");
2023-03-neotokyo/contracts/s1/NTItems.sol::1493 => require(_exists(tokenId), "ERC721Metadata: URI query for nonexistent token");
2023-03-neotokyo/contracts/s1/NTItems.sol::1505 => require(itemMintActive, "Minting is not currently active");
2023-03-neotokyo/contracts/s1/NTItems.sol::1506 => require(boxTokenId > 0 && boxTokenId < 2501, "That is not a claimable item");
2023-03-neotokyo/contracts/s1/NTItems.sol::1507 => require(whitelistValidated(toString(boxTokenId), spotOnLeaderboard, spotInWhitelist, proof), "That box cannot claim that item");
2023-03-neotokyo/contracts/s1/NTItems.sol::1508 => require(boxValidated(boxTokenId), "You are not the owner of that vault box");
2023-03-neotokyo/contracts/s1/NTItems.sol::1540 => require(address(bytesContract) != address(0), "Items cannot be bought yet");
2023-03-neotokyo/contracts/s1/NTItems.sol::1549 => require(tokenId > 0 && tokenId < 2501, "Token ID invalid");
2023-03-neotokyo/contracts/s1/NTItems.sol::1550 => require(!_exists(tokenId), "That token already exists");
2023-03-neotokyo/contracts/s1/NTItems.sol::1551 => require(_boxClaims[boxId] == 0, "That box has already claimed");
2023-03-neotokyo/contracts/s1/NTItems.sol::1558 => require(_boxClaims[boxId] == 0, "This box has minted");
2023-03-neotokyo/contracts/s1/NTLandDeploy.sol::235 => require(value == 0, "Strings: hex length insufficient");
2023-03-neotokyo/contracts/s1/NTLandDeploy.sol::297 => require(owner() == _msgSender(), "Ownable: caller is not the owner");
2023-03-neotokyo/contracts/s1/NTLandDeploy.sol::317 => require(newOwner != address(0), "Ownable: new owner is the zero address");
2023-03-neotokyo/contracts/s1/NTLandDeploy.sol::375 => require(_status != _ENTERED, "ReentrancyGuard: reentrant call");
2023-03-neotokyo/contracts/s1/NTLandDeploy.sol::486 => require(address(this).balance >= amount, "Address: insufficient balance");
2023-03-neotokyo/contracts/s1/NTLandDeploy.sol::489 => require(success, "Address: unable to send value, recipient may have reverted");
2023-03-neotokyo/contracts/s1/NTLandDeploy.sol::559 => require(address(this).balance >= value, "Address: insufficient balance for call");
2023-03-neotokyo/contracts/s1/NTLandDeploy.sol::560 => require(isContract(target), "Address: call to non-contract");
2023-03-neotokyo/contracts/s1/NTLandDeploy.sol::587 => require(isContract(target), "Address: static call to non-contract");
2023-03-neotokyo/contracts/s1/NTLandDeploy.sol::614 => require(isContract(target), "Address: delegate call to non-contract");
2023-03-neotokyo/contracts/s1/NTLandDeploy.sol::719 => require(owner != address(0), "ERC721: balance query for the zero address");
2023-03-neotokyo/contracts/s1/NTLandDeploy.sol::728 => require(owner != address(0), "ERC721: owner query for nonexistent token");
2023-03-neotokyo/contracts/s1/NTLandDeploy.sol::750 => require(_exists(tokenId), "ERC721Metadata: URI query for nonexistent token");
2023-03-neotokyo/contracts/s1/NTLandDeploy.sol::770 => require(to != owner, "ERC721: approval to current owner");
2023-03-neotokyo/contracts/s1/NTLandDeploy.sol::784 => require(_exists(tokenId), "ERC721: approved query for nonexistent token");
2023-03-neotokyo/contracts/s1/NTLandDeploy.sol::793 => require(operator != _msgSender(), "ERC721: approve to caller");
2023-03-neotokyo/contracts/s1/NTLandDeploy.sol::815 => require(_isApprovedOrOwner(_msgSender(), tokenId), "ERC721: transfer caller is not owner nor approved");
2023-03-neotokyo/contracts/s1/NTLandDeploy.sol::840 => require(_isApprovedOrOwner(_msgSender(), tokenId), "ERC721: transfer caller is not owner nor approved");
2023-03-neotokyo/contracts/s1/NTLandDeploy.sol::869 => require(_checkOnERC721Received(from, to, tokenId, _data), "ERC721: transfer to non ERC721Receiver implementer");
2023-03-neotokyo/contracts/s1/NTLandDeploy.sol::892 => require(_exists(tokenId), "ERC721: operator query for nonexistent token");
2023-03-neotokyo/contracts/s1/NTLandDeploy.sol::940 => require(to != address(0), "ERC721: mint to the zero address");
2023-03-neotokyo/contracts/s1/NTLandDeploy.sol::941 => require(!_exists(tokenId), "ERC721: token already minted");
2023-03-neotokyo/contracts/s1/NTLandDeploy.sol::991 => require(ERC721.ownerOf(tokenId) == from, "ERC721: transfer of token that is not own");
2023-03-neotokyo/contracts/s1/NTLandDeploy.sol::992 => require(to != address(0), "ERC721: transfer to the zero address");
2023-03-neotokyo/contracts/s1/NTLandDeploy.sol::1125 => require(index < ERC721.balanceOf(owner), "ERC721Enumerable: owner index out of bounds");
2023-03-neotokyo/contracts/s1/NTLandDeploy.sol::1140 => require(index < ERC721Enumerable.totalSupply(), "ERC721Enumerable: global index out of bounds");
2023-03-neotokyo/contracts/s1/NTLandDeploy.sol::1298 => require(_exists(tokenId), "ERC721Metadata: URI query for nonexistent token");
2023-03-neotokyo/contracts/s1/NTLandDeploy.sol::1328 => require(_exists(tokenId), "ERC721Metadata: URI query for nonexistent token");
2023-03-neotokyo/contracts/s1/NTLandDeploy.sol::1339 => require(landMintActive, "Minting is not currently active");
2023-03-neotokyo/contracts/s1/NTLandDeploy.sol::1340 => require(vaultTokenId > 0 && vaultTokenId < 2501, "That vault cannot claim land");
2023-03-neotokyo/contracts/s1/NTLandDeploy.sol::1341 => require(whitelistValidated(toString(vaultTokenId), spotOnLeaderboard, spotInWhitelist, proof), "That vault cannot claim that item");
2023-03-neotokyo/contracts/s1/NTLandDeploy.sol::1342 => require(vaultValidated(vaultTokenId), "You are not the owner of that vault box");
2023-03-neotokyo/contracts/s1/NTLandDeploy.sol::1343 => require(identityValidated(identityTokenId), "You are not the owner of that identity");
2023-03-neotokyo/contracts/s1/NTLandDeploy.sol::1344 => require(itemCacheValidated(itemCacheTokenId), "You are not the owner of that item cache");
2023-03-neotokyo/contracts/s1/NTLandDeploy.sol::1365 => require(address(bytesContract) != address(0), "Land cannot be bought yet");
2023-03-neotokyo/contracts/s1/NTLandDeploy.sol::1374 => require(tokenId > 0 && tokenId < 2501, "Token ID invalid");
2023-03-neotokyo/contracts/s1/NTLandDeploy.sol::1375 => require(!_exists(tokenId), "That token already exists");
2023-03-neotokyo/contracts/s1/NTLandDeploy.sol::1376 => require(_vaultClaims[vaultId] == 0, "That vault has already claimed");
2023-03-neotokyo/contracts/s1/NTLandDeploy.sol::1383 => require(_vaultClaims[vaultId] == 0, "This vault has minted");
2023-03-neotokyo/contracts/s1/NTLandDeploy.sol::1389 => require(_identityClaims[identityId] == 0, "This identity has minted");
2023-03-neotokyo/contracts/s1/NTLandDeploy.sol::1395 => require(_itemCacheClaims[itemCacheId] == 0, "This item cache has minted");
2023-03-neotokyo/contracts/s1/beckLoot.sol::232 => require(value == 0, "Strings: hex length insufficient");
2023-03-neotokyo/contracts/s1/beckLoot.sol::294 => require(owner() == _msgSender(), "Ownable: caller is not the owner");
2023-03-neotokyo/contracts/s1/beckLoot.sol::314 => require(newOwner != address(0), "Ownable: new owner is the zero address");
2023-03-neotokyo/contracts/s1/beckLoot.sol::372 => require(_status != _ENTERED, "ReentrancyGuard: reentrant call");
2023-03-neotokyo/contracts/s1/beckLoot.sol::483 => require(address(this).balance >= amount, "Address: insufficient balance");
2023-03-neotokyo/contracts/s1/beckLoot.sol::486 => require(success, "Address: unable to send value, recipient may have reverted");
2023-03-neotokyo/contracts/s1/beckLoot.sol::556 => require(address(this).balance >= value, "Address: insufficient balance for call");
2023-03-neotokyo/contracts/s1/beckLoot.sol::557 => require(isContract(target), "Address: call to non-contract");
2023-03-neotokyo/contracts/s1/beckLoot.sol::584 => require(isContract(target), "Address: static call to non-contract");
2023-03-neotokyo/contracts/s1/beckLoot.sol::611 => require(isContract(target), "Address: delegate call to non-contract");
2023-03-neotokyo/contracts/s1/beckLoot.sol::716 => require(owner != address(0), "ERC721: balance query for the zero address");
2023-03-neotokyo/contracts/s1/beckLoot.sol::725 => require(owner != address(0), "ERC721: owner query for nonexistent token");
2023-03-neotokyo/contracts/s1/beckLoot.sol::747 => require(_exists(tokenId), "ERC721Metadata: URI query for nonexistent token");
2023-03-neotokyo/contracts/s1/beckLoot.sol::767 => require(to != owner, "ERC721: approval to current owner");
2023-03-neotokyo/contracts/s1/beckLoot.sol::781 => require(_exists(tokenId), "ERC721: approved query for nonexistent token");
2023-03-neotokyo/contracts/s1/beckLoot.sol::790 => require(operator != _msgSender(), "ERC721: approve to caller");
2023-03-neotokyo/contracts/s1/beckLoot.sol::812 => require(_isApprovedOrOwner(_msgSender(), tokenId), "ERC721: transfer caller is not owner nor approved");
2023-03-neotokyo/contracts/s1/beckLoot.sol::837 => require(_isApprovedOrOwner(_msgSender(), tokenId), "ERC721: transfer caller is not owner nor approved");
2023-03-neotokyo/contracts/s1/beckLoot.sol::866 => require(_checkOnERC721Received(from, to, tokenId, _data), "ERC721: transfer to non ERC721Receiver implementer");
2023-03-neotokyo/contracts/s1/beckLoot.sol::889 => require(_exists(tokenId), "ERC721: operator query for nonexistent token");
2023-03-neotokyo/contracts/s1/beckLoot.sol::937 => require(to != address(0), "ERC721: mint to the zero address");
2023-03-neotokyo/contracts/s1/beckLoot.sol::938 => require(!_exists(tokenId), "ERC721: token already minted");
2023-03-neotokyo/contracts/s1/beckLoot.sol::988 => require(ERC721.ownerOf(tokenId) == from, "ERC721: transfer of token that is not own");
2023-03-neotokyo/contracts/s1/beckLoot.sol::989 => require(to != address(0), "ERC721: transfer to the zero address");
2023-03-neotokyo/contracts/s1/beckLoot.sol::1122 => require(index < ERC721.balanceOf(owner), "ERC721Enumerable: owner index out of bounds");
2023-03-neotokyo/contracts/s1/beckLoot.sol::1137 => require(index < ERC721Enumerable.totalSupply(), "ERC721Enumerable: global index out of bounds");
2023-03-neotokyo/contracts/s1/beckLoot.sol::1368 => require(_exists(tokenId), "ERC721Metadata: URI query for nonexistent token");
2023-03-neotokyo/contracts/s1/beckLoot.sol::1378 => require(_exists(tokenId), "ERC721Metadata: URI query for nonexistent token");
2023-03-neotokyo/contracts/s1/beckLoot.sol::1388 => require(_exists(tokenId), "ERC721Metadata: URI query for nonexistent token");
2023-03-neotokyo/contracts/s1/beckLoot.sol::1398 => require(_exists(tokenId), "ERC721Metadata: URI query for nonexistent token");
2023-03-neotokyo/contracts/s1/beckLoot.sol::1408 => require(_exists(tokenId), "ERC721Metadata: URI query for nonexistent token");
2023-03-neotokyo/contracts/s1/beckLoot.sol::1418 => require(_exists(tokenId), "ERC721Metadata: URI query for nonexistent token");
2023-03-neotokyo/contracts/s1/beckLoot.sol::1428 => require(_exists(tokenId), "ERC721Metadata: URI query for nonexistent token");
2023-03-neotokyo/contracts/s1/beckLoot.sol::1438 => require(_exists(tokenId), "ERC721Metadata: URI query for nonexistent token");
2023-03-neotokyo/contracts/s1/beckLoot.sol::1448 => require(_exists(tokenId), "ERC721Metadata: URI query for nonexistent token");
2023-03-neotokyo/contracts/s1/beckLoot.sol::1458 => require(_exists(tokenId), "ERC721Metadata: URI query for nonexistent token");
2023-03-neotokyo/contracts/s1/beckLoot.sol::1468 => require(_exists(tokenId), "ERC721Metadata: URI query for nonexistent token");
2023-03-neotokyo/contracts/s1/beckLoot.sol::1478 => require(_exists(tokenId), "ERC721Metadata: URI query for nonexistent token");
2023-03-neotokyo/contracts/s1/beckLoot.sol::1489 => require(tokenId > 0 && tokenId < 2001, "Token ID invalid");
2023-03-neotokyo/contracts/s1/beckLoot.sol::1490 => require(saleActive, "Public claiming is currently closed");
2023-03-neotokyo/contracts/s1/beckLoot.sol::1495 => require(whitelistActive, "The whitelist is not active yet");
2023-03-neotokyo/contracts/s1/beckLoot.sol::1496 => require(tokenId > 0 && tokenId < 2001, "Token ID invalid");
2023-03-neotokyo/contracts/s1/beckLoot.sol::1497 => require(whitelistValidated(_msgSender(), spotInWhitelist, proof), "You're not on the whitelist");
2023-03-neotokyo/contracts/s1/beckLoot.sol::1498 => require(_previousMinters[_msgSender()] < 1, "You've already minted");
2023-03-neotokyo/contracts/s1/beckLoot.sol::1507 => require(tokenId > 2000 && tokenId < 2251, "Token ID invalid");
2023-03-neotokyo/contracts/s1/beckLoot.sol::1512 => require(tokenId > 2250 && tokenId < 2281, "Token ID invalid");
2023-03-neotokyo/contracts/s1/beckLoot.sol::1517 => require(tokenId > 2280 && tokenId < 2288, "Token ID invalid");
2023-03-neotokyo/contracts/s1/vaultBox.sol::232 => require(value == 0, "Strings: hex length insufficient");
2023-03-neotokyo/contracts/s1/vaultBox.sol::294 => require(owner() == _msgSender(), "Ownable: caller is not the owner");
2023-03-neotokyo/contracts/s1/vaultBox.sol::314 => require(newOwner != address(0), "Ownable: new owner is the zero address");
2023-03-neotokyo/contracts/s1/vaultBox.sol::372 => require(_status != _ENTERED, "ReentrancyGuard: reentrant call");
2023-03-neotokyo/contracts/s1/vaultBox.sol::483 => require(address(this).balance >= amount, "Address: insufficient balance");
2023-03-neotokyo/contracts/s1/vaultBox.sol::486 => require(success, "Address: unable to send value, recipient may have reverted");
2023-03-neotokyo/contracts/s1/vaultBox.sol::556 => require(address(this).balance >= value, "Address: insufficient balance for call");
2023-03-neotokyo/contracts/s1/vaultBox.sol::557 => require(isContract(target), "Address: call to non-contract");
2023-03-neotokyo/contracts/s1/vaultBox.sol::584 => require(isContract(target), "Address: static call to non-contract");
2023-03-neotokyo/contracts/s1/vaultBox.sol::611 => require(isContract(target), "Address: delegate call to non-contract");
2023-03-neotokyo/contracts/s1/vaultBox.sol::716 => require(owner != address(0), "ERC721: balance query for the zero address");
2023-03-neotokyo/contracts/s1/vaultBox.sol::725 => require(owner != address(0), "ERC721: owner query for nonexistent token");
2023-03-neotokyo/contracts/s1/vaultBox.sol::747 => require(_exists(tokenId), "ERC721Metadata: URI query for nonexistent token");
2023-03-neotokyo/contracts/s1/vaultBox.sol::767 => require(to != owner, "ERC721: approval to current owner");
2023-03-neotokyo/contracts/s1/vaultBox.sol::781 => require(_exists(tokenId), "ERC721: approved query for nonexistent token");
2023-03-neotokyo/contracts/s1/vaultBox.sol::790 => require(operator != _msgSender(), "ERC721: approve to caller");
2023-03-neotokyo/contracts/s1/vaultBox.sol::812 => require(_isApprovedOrOwner(_msgSender(), tokenId), "ERC721: transfer caller is not owner nor approved");
2023-03-neotokyo/contracts/s1/vaultBox.sol::837 => require(_isApprovedOrOwner(_msgSender(), tokenId), "ERC721: transfer caller is not owner nor approved");
2023-03-neotokyo/contracts/s1/vaultBox.sol::866 => require(_checkOnERC721Received(from, to, tokenId, _data), "ERC721: transfer to non ERC721Receiver implementer");
2023-03-neotokyo/contracts/s1/vaultBox.sol::889 => require(_exists(tokenId), "ERC721: operator query for nonexistent token");
2023-03-neotokyo/contracts/s1/vaultBox.sol::937 => require(to != address(0), "ERC721: mint to the zero address");
2023-03-neotokyo/contracts/s1/vaultBox.sol::938 => require(!_exists(tokenId), "ERC721: token already minted");
2023-03-neotokyo/contracts/s1/vaultBox.sol::988 => require(ERC721.ownerOf(tokenId) == from, "ERC721: transfer of token that is not own");
2023-03-neotokyo/contracts/s1/vaultBox.sol::989 => require(to != address(0), "ERC721: transfer to the zero address");
2023-03-neotokyo/contracts/s1/vaultBox.sol::1122 => require(index < ERC721.balanceOf(owner), "ERC721Enumerable: owner index out of bounds");
2023-03-neotokyo/contracts/s1/vaultBox.sol::1137 => require(index < ERC721Enumerable.totalSupply(), "ERC721Enumerable: global index out of bounds");
2023-03-neotokyo/contracts/s1/vaultBox.sol::1355 => require(_exists(tokenId), "ERC721Metadata: URI query for nonexistent token");
2023-03-neotokyo/contracts/s1/vaultBox.sol::1366 => require(_exists(tokenId), "ERC721Metadata: URI query for nonexistent token");
2023-03-neotokyo/contracts/s1/vaultBox.sol::1376 => require(_exists(tokenId), "ERC721Metadata: URI query for nonexistent token");
2023-03-neotokyo/contracts/s1/vaultBox.sol::1386 => require(_exists(tokenId), "ERC721Metadata: URI query for nonexistent token");
2023-03-neotokyo/contracts/s1/vaultBox.sol::1404 => require(_exists(tokenId), "ERC721Metadata: URI query for nonexistent token");
2023-03-neotokyo/contracts/s1/vaultBox.sol::1425 => require(tokenId > 0 && tokenId < 2501, "Token ID invalid");
2023-03-neotokyo/contracts/s1/vaultBox.sol::1426 => require(saleActive, "Holder claiming is currently closed");
2023-03-neotokyo/contracts/s1/vaultBox.sol::1428 => require(identityTokenId > 0, "You have no identities capable of minting");
2023-03-neotokyo/contracts/s1/vaultBox.sol::1441 => require(whitelistActive, "The whitelist is not active yet");
2023-03-neotokyo/contracts/s1/vaultBox.sol::1442 => require(tokenId > 0 && tokenId < 2501, "Token ID invalid");
2023-03-neotokyo/contracts/s1/vaultBox.sol::1443 => require(whitelistValidated(_msgSender(), spotInWhitelist, proof), "You're not on the whitelist");
2023-03-neotokyo/contracts/s1/vaultBox.sol::1444 => require(_whitelistMinters[_msgSender()] < 1, "You've already minted");
2023-03-neotokyo/contracts/s1/vaultBox.sol::1453 => require(tokenId > 0 && tokenId < 2501, "Token ID invalid");
2023-03-neotokyo/contracts/s1/vaultBox.sol::1454 => require(openClaimActive, "Public claiming is currently closed");
2023-03-neotokyo/contracts/s1/vaultBox.sol::1460 => require(tokenId > 0 && tokenId < 2501, "Token ID invalid");
2023-03-neotokyo/contracts/s2/NTOuterCitizenDeploy.sol::251 => require(value == 0, "Strings: hex length insufficient");
2023-03-neotokyo/contracts/s2/NTOuterCitizenDeploy.sol::313 => require(owner() == _msgSender(), "Ownable: caller is not the owner");
2023-03-neotokyo/contracts/s2/NTOuterCitizenDeploy.sol::333 => require(newOwner != address(0), "Ownable: new owner is the zero address");
2023-03-neotokyo/contracts/s2/NTOuterCitizenDeploy.sol::391 => require(_status != _ENTERED, "ReentrancyGuard: reentrant call");
2023-03-neotokyo/contracts/s2/NTOuterCitizenDeploy.sol::502 => require(address(this).balance >= amount, "Address: insufficient balance");
2023-03-neotokyo/contracts/s2/NTOuterCitizenDeploy.sol::505 => require(success, "Address: unable to send value, recipient may have reverted");
2023-03-neotokyo/contracts/s2/NTOuterCitizenDeploy.sol::575 => require(address(this).balance >= value, "Address: insufficient balance for call");
2023-03-neotokyo/contracts/s2/NTOuterCitizenDeploy.sol::576 => require(isContract(target), "Address: call to non-contract");
2023-03-neotokyo/contracts/s2/NTOuterCitizenDeploy.sol::603 => require(isContract(target), "Address: static call to non-contract");
2023-03-neotokyo/contracts/s2/NTOuterCitizenDeploy.sol::630 => require(isContract(target), "Address: delegate call to non-contract");
2023-03-neotokyo/contracts/s2/NTOuterCitizenDeploy.sol::735 => require(owner != address(0), "ERC721: balance query for the zero address");
2023-03-neotokyo/contracts/s2/NTOuterCitizenDeploy.sol::744 => require(owner != address(0), "ERC721: owner query for nonexistent token");
2023-03-neotokyo/contracts/s2/NTOuterCitizenDeploy.sol::766 => require(_exists(tokenId), "ERC721Metadata: URI query for nonexistent token");
2023-03-neotokyo/contracts/s2/NTOuterCitizenDeploy.sol::786 => require(to != owner, "ERC721: approval to current owner");
2023-03-neotokyo/contracts/s2/NTOuterCitizenDeploy.sol::800 => require(_exists(tokenId), "ERC721: approved query for nonexistent token");
2023-03-neotokyo/contracts/s2/NTOuterCitizenDeploy.sol::809 => require(operator != _msgSender(), "ERC721: approve to caller");
2023-03-neotokyo/contracts/s2/NTOuterCitizenDeploy.sol::831 => require(_isApprovedOrOwner(_msgSender(), tokenId), "ERC721: transfer caller is not owner nor approved");
2023-03-neotokyo/contracts/s2/NTOuterCitizenDeploy.sol::856 => require(_isApprovedOrOwner(_msgSender(), tokenId), "ERC721: transfer caller is not owner nor approved");
2023-03-neotokyo/contracts/s2/NTOuterCitizenDeploy.sol::885 => require(_checkOnERC721Received(from, to, tokenId, _data), "ERC721: transfer to non ERC721Receiver implementer");
2023-03-neotokyo/contracts/s2/NTOuterCitizenDeploy.sol::908 => require(_exists(tokenId), "ERC721: operator query for nonexistent token");
2023-03-neotokyo/contracts/s2/NTOuterCitizenDeploy.sol::956 => require(to != address(0), "ERC721: mint to the zero address");
2023-03-neotokyo/contracts/s2/NTOuterCitizenDeploy.sol::957 => require(!_exists(tokenId), "ERC721: token already minted");
2023-03-neotokyo/contracts/s2/NTOuterCitizenDeploy.sol::1007 => require(ERC721.ownerOf(tokenId) == from, "ERC721: transfer of token that is not own");
2023-03-neotokyo/contracts/s2/NTOuterCitizenDeploy.sol::1008 => require(to != address(0), "ERC721: transfer to the zero address");
2023-03-neotokyo/contracts/s2/NTOuterCitizenDeploy.sol::1141 => require(index < ERC721.balanceOf(owner), "ERC721Enumerable: owner index out of bounds");
2023-03-neotokyo/contracts/s2/NTOuterCitizenDeploy.sol::1156 => require(index < ERC721Enumerable.totalSupply(), "ERC721Enumerable: global index out of bounds");
2023-03-neotokyo/contracts/s2/NTOuterCitizenDeploy.sol::1333 => require(_exists(tokenId), "ERC721Metadata: URI query for nonexistent token");
2023-03-neotokyo/contracts/s2/NTOuterCitizenDeploy.sol::1353 => require(citizenMintActive, "Uploading is not currently active");
2023-03-neotokyo/contracts/s2/NTOuterCitizenDeploy.sol::1354 => require(identityValidated(identityId), "You are not the owner of that identity");
2023-03-neotokyo/contracts/s2/NTOuterCitizenDeploy.sol::1355 => require(itemCacheValidated(itemCacheId), "You are not the owner of that item cache");
2023-03-neotokyo/contracts/s2/NTOuterCitizenDeploy.sol::1356 => require(landDeedValidated(landDeedId), "You are not the owner of that land deed");
2023-03-neotokyo/contracts/s2/NTOuterCitizenDeploy.sol::1360 => require(femaleActive, "Females cannot be uploaded yet");
2023-03-neotokyo/contracts/s2/NTOuterCitizenDeploy.sol::1402 => require(ownerOf(citizenId) == _msgSender(), "You do not own that citizen");
2023-03-neotokyo/contracts/s2/NTOuterCitizenDeploy.sol::1432 => require(femaleActive, "Females cannot be uploaded yet");
2023-03-neotokyo/contracts/s2/NTOuterCitizenDeploy.sol::1433 => require(ownerOf(tokenId) == _msgSender(), "You do not own that citizen");
2023-03-neotokyo/contracts/s2/NTOuterCitizenDeploy.sol::1441 => require(ownerOf(tokenId) == _msgSender(), "You do not own that citizen");
2023-03-neotokyo/contracts/s2/NTOuterCitizenDeploy.sol::1525 => require(boughtIdentitiesActive, "Identities cannot be bought yet");
2023-03-neotokyo/contracts/s2/NTOuterCitizenDeploy.sol::1526 => require(address(boughtIdentityContract) != address(0), "Identity contract not set");
2023-03-neotokyo/contracts/s2/NTOuterIdentity.sol::236 => require(value == 0, "Strings: hex length insufficient");
2023-03-neotokyo/contracts/s2/NTOuterIdentity.sol::298 => require(owner() == _msgSender(), "Ownable: caller is not the owner");
2023-03-neotokyo/contracts/s2/NTOuterIdentity.sol::318 => require(newOwner != address(0), "Ownable: new owner is the zero address");
2023-03-neotokyo/contracts/s2/NTOuterIdentity.sol::376 => require(_status != _ENTERED, "ReentrancyGuard: reentrant call");
2023-03-neotokyo/contracts/s2/NTOuterIdentity.sol::487 => require(address(this).balance >= amount, "Address: insufficient balance");
2023-03-neotokyo/contracts/s2/NTOuterIdentity.sol::490 => require(success, "Address: unable to send value, recipient may have reverted");
2023-03-neotokyo/contracts/s2/NTOuterIdentity.sol::560 => require(address(this).balance >= value, "Address: insufficient balance for call");
2023-03-neotokyo/contracts/s2/NTOuterIdentity.sol::561 => require(isContract(target), "Address: call to non-contract");
2023-03-neotokyo/contracts/s2/NTOuterIdentity.sol::588 => require(isContract(target), "Address: static call to non-contract");
2023-03-neotokyo/contracts/s2/NTOuterIdentity.sol::615 => require(isContract(target), "Address: delegate call to non-contract");
2023-03-neotokyo/contracts/s2/NTOuterIdentity.sol::720 => require(owner != address(0), "ERC721: balance query for the zero address");
2023-03-neotokyo/contracts/s2/NTOuterIdentity.sol::729 => require(owner != address(0), "ERC721: owner query for nonexistent token");
2023-03-neotokyo/contracts/s2/NTOuterIdentity.sol::751 => require(_exists(tokenId), "ERC721Metadata: URI query for nonexistent token");
2023-03-neotokyo/contracts/s2/NTOuterIdentity.sol::771 => require(to != owner, "ERC721: approval to current owner");
2023-03-neotokyo/contracts/s2/NTOuterIdentity.sol::785 => require(_exists(tokenId), "ERC721: approved query for nonexistent token");
2023-03-neotokyo/contracts/s2/NTOuterIdentity.sol::794 => require(operator != _msgSender(), "ERC721: approve to caller");
2023-03-neotokyo/contracts/s2/NTOuterIdentity.sol::816 => require(_isApprovedOrOwner(_msgSender(), tokenId), "ERC721: transfer caller is not owner nor approved");
2023-03-neotokyo/contracts/s2/NTOuterIdentity.sol::841 => require(_isApprovedOrOwner(_msgSender(), tokenId), "ERC721: transfer caller is not owner nor approved");
2023-03-neotokyo/contracts/s2/NTOuterIdentity.sol::870 => require(_checkOnERC721Received(from, to, tokenId, _data), "ERC721: transfer to non ERC721Receiver implementer");
2023-03-neotokyo/contracts/s2/NTOuterIdentity.sol::893 => require(_exists(tokenId), "ERC721: operator query for nonexistent token");
2023-03-neotokyo/contracts/s2/NTOuterIdentity.sol::941 => require(to != address(0), "ERC721: mint to the zero address");
2023-03-neotokyo/contracts/s2/NTOuterIdentity.sol::942 => require(!_exists(tokenId), "ERC721: token already minted");
2023-03-neotokyo/contracts/s2/NTOuterIdentity.sol::992 => require(ERC721.ownerOf(tokenId) == from, "ERC721: transfer of token that is not own");
2023-03-neotokyo/contracts/s2/NTOuterIdentity.sol::993 => require(to != address(0), "ERC721: transfer to the zero address");
2023-03-neotokyo/contracts/s2/NTOuterIdentity.sol::1126 => require(index < ERC721.balanceOf(owner), "ERC721Enumerable: owner index out of bounds");
2023-03-neotokyo/contracts/s2/NTOuterIdentity.sol::1141 => require(index < ERC721Enumerable.totalSupply(), "ERC721Enumerable: global index out of bounds");
2023-03-neotokyo/contracts/s2/NTOuterIdentity.sol::1285 => require(!saleActive, "Sale is already active");
2023-03-neotokyo/contracts/s2/NTOuterIdentity.sol::1327 => require(!burnActive, "Burn already started");
2023-03-neotokyo/contracts/s2/NTOuterIdentity.sol::1334 => require(burnActive, "Burn not active");
2023-03-neotokyo/contracts/s2/NTOuterIdentity.sol::1339 => require(_exists(tokenId), "ERC721Metadata: URI query for nonexistent token");
2023-03-neotokyo/contracts/s2/NTOuterIdentity.sol::1349 => require(_exists(tokenId), "ERC721Metadata: URI query for nonexistent token");
2023-03-neotokyo/contracts/s2/NTOuterIdentity.sol::1359 => require(_exists(tokenId), "ERC721Metadata: URI query for nonexistent token");
2023-03-neotokyo/contracts/s2/NTOuterIdentity.sol::1369 => require(_exists(tokenId), "ERC721Metadata: URI query for nonexistent token");
2023-03-neotokyo/contracts/s2/NTOuterIdentity.sol::1379 => require(_exists(tokenId), "ERC721Metadata: URI query for nonexistent token");
2023-03-neotokyo/contracts/s2/NTOuterIdentity.sol::1389 => require(_exists(tokenId), "ERC721Metadata: URI query for nonexistent token");
2023-03-neotokyo/contracts/s2/NTOuterIdentity.sol::1399 => require(_exists(tokenId), "ERC721Metadata: URI query for nonexistent token");
2023-03-neotokyo/contracts/s2/NTOuterIdentity.sol::1409 => require(_exists(tokenId), "ERC721Metadata: URI query for nonexistent token");
2023-03-neotokyo/contracts/s2/NTOuterIdentity.sol::1419 => require(_exists(tokenId), "ERC721Metadata: URI query for nonexistent token");
2023-03-neotokyo/contracts/s2/NTOuterIdentity.sol::1429 => require(_exists(tokenId), "ERC721Metadata: URI query for nonexistent token");
2023-03-neotokyo/contracts/s2/NTOuterIdentity.sol::1439 => require(_exists(tokenId), "ERC721Metadata: URI query for nonexistent token");
2023-03-neotokyo/contracts/s2/NTOuterIdentity.sol::1449 => require(_exists(tokenId), "ERC721Metadata: URI query for nonexistent token");
2023-03-neotokyo/contracts/s2/NTOuterIdentity.sol::1461 => require(burnActive, "Burning is not active");
2023-03-neotokyo/contracts/s2/NTOuterIdentity.sol::1462 => require(block.timestamp < (beginBurn + timeToBurn), "Burning has ended");
2023-03-neotokyo/contracts/s2/NTOuterIdentity.sol::1468 => require(saleActive, "Private minting has not started");
2023-03-neotokyo/contracts/s2/NTOuterIdentity.sol::1469 => require(block.timestamp < (beginReserve + earlyReservePeriod), "Public minting is open");
2023-03-neotokyo/contracts/s2/NTOuterIdentity.sol::1474 => require(saleActive, "Reserving is currently closed");
2023-03-neotokyo/contracts/s2/NTOuterIdentity.sol::1475 => require(block.timestamp > (beginReserve + earlyReservePeriod) || validateCitizen(), "You cannot reserve yet");
2023-03-neotokyo/contracts/s2/NTOuterIdentity.sol::1476 => require(totalMembersInBytesAuction < maxTokens, "All tokens have been reserved");
2023-03-neotokyo/contracts/s2/NTOuterIdentity.sol::1477 => require(!addressHasReserved[_msgSender()], "This wallet has already reserved");
2023-03-neotokyo/contracts/s2/NTOuterIdentity.sol::1478 => require(msg.value == costToReserve, "Incorrect reserve payment");
2023-03-neotokyo/contracts/s2/NTOuterIdentity.sol::1499 => require(burnActive, "Burning is not currently active");
2023-03-neotokyo/contracts/s2/NTOuterIdentity.sol::1500 => require(block.timestamp < (timeToBurn + beginBurn), "Burn auction has ended");
2023-03-neotokyo/contracts/s2/NTOuterIdentity.sol::1501 => require(addressHasReserved[_msgSender()], "You did not enter the auction");
2023-03-neotokyo/contracts/s2/NTOuterIdentity.sol::1515 => require(whitelistActive, "The whitelist is not active yet");
2023-03-neotokyo/contracts/s2/NTOuterIdentity.sol::1516 => require(!_exists(tokenId), "Token ID already exists");
2023-03-neotokyo/contracts/s2/NTOuterIdentity.sol::1517 => require(whitelistValidated(_msgSender(), spotInWhitelist, tokenId, proof), "You're not on the whitelist");
2023-03-neotokyo/contracts/s2/NTOuterIdentity.sol::1518 => require(_previousMinters[_msgSender()] < 1, "You've already minted");
2023-03-neotokyo/contracts/s2/NTOuterIdentity.sol::1527 => require(!_exists(tokenId), "Token ID already exists");
2023-03-neotokyo/contracts/s2/NTOuterIdentity.sol::1569 => require(withdrawalAddress != address(0), "Withdrawal address must be set");
2023-03-neotokyo/contracts/s2/NTS2Items.sol::235 => require(value == 0, "Strings: hex length insufficient");
2023-03-neotokyo/contracts/s2/NTS2Items.sol::297 => require(owner() == _msgSender(), "Ownable: caller is not the owner");
2023-03-neotokyo/contracts/s2/NTS2Items.sol::317 => require(newOwner != address(0), "Ownable: new owner is the zero address");
2023-03-neotokyo/contracts/s2/NTS2Items.sol::375 => require(_status != _ENTERED, "ReentrancyGuard: reentrant call");
2023-03-neotokyo/contracts/s2/NTS2Items.sol::486 => require(address(this).balance >= amount, "Address: insufficient balance");
2023-03-neotokyo/contracts/s2/NTS2Items.sol::489 => require(success, "Address: unable to send value, recipient may have reverted");
2023-03-neotokyo/contracts/s2/NTS2Items.sol::559 => require(address(this).balance >= value, "Address: insufficient balance for call");
2023-03-neotokyo/contracts/s2/NTS2Items.sol::560 => require(isContract(target), "Address: call to non-contract");
2023-03-neotokyo/contracts/s2/NTS2Items.sol::587 => require(isContract(target), "Address: static call to non-contract");
2023-03-neotokyo/contracts/s2/NTS2Items.sol::614 => require(isContract(target), "Address: delegate call to non-contract");
2023-03-neotokyo/contracts/s2/NTS2Items.sol::719 => require(owner != address(0), "ERC721: balance query for the zero address");
2023-03-neotokyo/contracts/s2/NTS2Items.sol::728 => require(owner != address(0), "ERC721: owner query for nonexistent token");
2023-03-neotokyo/contracts/s2/NTS2Items.sol::750 => require(_exists(tokenId), "ERC721Metadata: URI query for nonexistent token");
2023-03-neotokyo/contracts/s2/NTS2Items.sol::770 => require(to != owner, "ERC721: approval to current owner");
2023-03-neotokyo/contracts/s2/NTS2Items.sol::784 => require(_exists(tokenId), "ERC721: approved query for nonexistent token");
2023-03-neotokyo/contracts/s2/NTS2Items.sol::793 => require(operator != _msgSender(), "ERC721: approve to caller");
2023-03-neotokyo/contracts/s2/NTS2Items.sol::815 => require(_isApprovedOrOwner(_msgSender(), tokenId), "ERC721: transfer caller is not owner nor approved");
2023-03-neotokyo/contracts/s2/NTS2Items.sol::840 => require(_isApprovedOrOwner(_msgSender(), tokenId), "ERC721: transfer caller is not owner nor approved");
2023-03-neotokyo/contracts/s2/NTS2Items.sol::869 => require(_checkOnERC721Received(from, to, tokenId, _data), "ERC721: transfer to non ERC721Receiver implementer");
2023-03-neotokyo/contracts/s2/NTS2Items.sol::892 => require(_exists(tokenId), "ERC721: operator query for nonexistent token");
2023-03-neotokyo/contracts/s2/NTS2Items.sol::940 => require(to != address(0), "ERC721: mint to the zero address");
2023-03-neotokyo/contracts/s2/NTS2Items.sol::941 => require(!_exists(tokenId), "ERC721: token already minted");
2023-03-neotokyo/contracts/s2/NTS2Items.sol::991 => require(ERC721.ownerOf(tokenId) == from, "ERC721: transfer of token that is not own");
2023-03-neotokyo/contracts/s2/NTS2Items.sol::992 => require(to != address(0), "ERC721: transfer to the zero address");
2023-03-neotokyo/contracts/s2/NTS2Items.sol::1125 => require(index < ERC721.balanceOf(owner), "ERC721Enumerable: owner index out of bounds");
2023-03-neotokyo/contracts/s2/NTS2Items.sol::1140 => require(index < ERC721Enumerable.totalSupply(), "ERC721Enumerable: global index out of bounds");
2023-03-neotokyo/contracts/s2/NTS2Items.sol::1276 => require(_exists(tokenId), "ERC721Metadata: URI query for nonexistent token");
2023-03-neotokyo/contracts/s2/NTS2Items.sol::1287 => require(_exists(tokenId), "ERC721Metadata: URI query for nonexistent token");
2023-03-neotokyo/contracts/s2/NTS2Items.sol::1297 => require(_exists(tokenId), "ERC721Metadata: URI query for nonexistent token");
2023-03-neotokyo/contracts/s2/NTS2Items.sol::1307 => require(_exists(tokenId), "ERC721Metadata: URI query for nonexistent token");
2023-03-neotokyo/contracts/s2/NTS2Items.sol::1329 => require(_exists(tokenId), "ERC721Metadata: URI query for nonexistent token");
2023-03-neotokyo/contracts/s2/NTS2Items.sol::1341 => require(itemMintActive, "Minting is not currently active");
2023-03-neotokyo/contracts/s2/NTS2Items.sol::1342 => require(identityTokenId > 0 && identityTokenId < 4501, "That is not a claimable item");
2023-03-neotokyo/contracts/s2/NTS2Items.sol::1343 => require(whitelistValidated(identityTokenId, spotOnLeaderboard, spotInWhitelist, proof), "That identity cannot claim that item");
2023-03-neotokyo/contracts/s2/NTS2Items.sol::1344 => require(identityValidated(identityTokenId), "You are not the owner of that identity ");
2023-03-neotokyo/contracts/s2/NTS2Items.sol::1357 => require(address(bytesContract) != address(0), "Items cannot be bought yet");
2023-03-neotokyo/contracts/s2/NTS2Items.sol::1366 => require(tokenId > 0 && tokenId < 4501, "Token ID invalid");
2023-03-neotokyo/contracts/s2/NTS2Items.sol::1367 => require(!_exists(tokenId), "That token already exists");
2023-03-neotokyo/contracts/s2/NTS2Items.sol::1368 => require(_identityClaims[identityId] == 0, "That identity has already claimed");
2023-03-neotokyo/contracts/s2/NTS2Items.sol::1375 => require(_identityClaims[identityId] == 0, "This ID has minted");
2023-03-neotokyo/contracts/s2/NTS2LandDeploy.sol::235 => require(value == 0, "Strings: hex length insufficient");
2023-03-neotokyo/contracts/s2/NTS2LandDeploy.sol::297 => require(owner() == _msgSender(), "Ownable: caller is not the owner");
2023-03-neotokyo/contracts/s2/NTS2LandDeploy.sol::317 => require(newOwner != address(0), "Ownable: new owner is the zero address");
2023-03-neotokyo/contracts/s2/NTS2LandDeploy.sol::375 => require(_status != _ENTERED, "ReentrancyGuard: reentrant call");
2023-03-neotokyo/contracts/s2/NTS2LandDeploy.sol::486 => require(address(this).balance >= amount, "Address: insufficient balance");
2023-03-neotokyo/contracts/s2/NTS2LandDeploy.sol::489 => require(success, "Address: unable to send value, recipient may have reverted");
2023-03-neotokyo/contracts/s2/NTS2LandDeploy.sol::559 => require(address(this).balance >= value, "Address: insufficient balance for call");
2023-03-neotokyo/contracts/s2/NTS2LandDeploy.sol::560 => require(isContract(target), "Address: call to non-contract");
2023-03-neotokyo/contracts/s2/NTS2LandDeploy.sol::587 => require(isContract(target), "Address: static call to non-contract");
2023-03-neotokyo/contracts/s2/NTS2LandDeploy.sol::614 => require(isContract(target), "Address: delegate call to non-contract");
2023-03-neotokyo/contracts/s2/NTS2LandDeploy.sol::719 => require(owner != address(0), "ERC721: balance query for the zero address");
2023-03-neotokyo/contracts/s2/NTS2LandDeploy.sol::728 => require(owner != address(0), "ERC721: owner query for nonexistent token");
2023-03-neotokyo/contracts/s2/NTS2LandDeploy.sol::750 => require(_exists(tokenId), "ERC721Metadata: URI query for nonexistent token");
2023-03-neotokyo/contracts/s2/NTS2LandDeploy.sol::770 => require(to != owner, "ERC721: approval to current owner");
2023-03-neotokyo/contracts/s2/NTS2LandDeploy.sol::784 => require(_exists(tokenId), "ERC721: approved query for nonexistent token");
2023-03-neotokyo/contracts/s2/NTS2LandDeploy.sol::793 => require(operator != _msgSender(), "ERC721: approve to caller");
2023-03-neotokyo/contracts/s2/NTS2LandDeploy.sol::815 => require(_isApprovedOrOwner(_msgSender(), tokenId), "ERC721: transfer caller is not owner nor approved");
2023-03-neotokyo/contracts/s2/NTS2LandDeploy.sol::840 => require(_isApprovedOrOwner(_msgSender(), tokenId), "ERC721: transfer caller is not owner nor approved");
2023-03-neotokyo/contracts/s2/NTS2LandDeploy.sol::869 => require(_checkOnERC721Received(from, to, tokenId, _data), "ERC721: transfer to non ERC721Receiver implementer");
2023-03-neotokyo/contracts/s2/NTS2LandDeploy.sol::892 => require(_exists(tokenId), "ERC721: operator query for nonexistent token");
2023-03-neotokyo/contracts/s2/NTS2LandDeploy.sol::940 => require(to != address(0), "ERC721: mint to the zero address");
2023-03-neotokyo/contracts/s2/NTS2LandDeploy.sol::941 => require(!_exists(tokenId), "ERC721: token already minted");
2023-03-neotokyo/contracts/s2/NTS2LandDeploy.sol::991 => require(ERC721.ownerOf(tokenId) == from, "ERC721: transfer of token that is not own");
2023-03-neotokyo/contracts/s2/NTS2LandDeploy.sol::992 => require(to != address(0), "ERC721: transfer to the zero address");
2023-03-neotokyo/contracts/s2/NTS2LandDeploy.sol::1125 => require(index < ERC721.balanceOf(owner), "ERC721Enumerable: owner index out of bounds");
2023-03-neotokyo/contracts/s2/NTS2LandDeploy.sol::1140 => require(index < ERC721Enumerable.totalSupply(), "ERC721Enumerable: global index out of bounds");
2023-03-neotokyo/contracts/s2/NTS2LandDeploy.sol::1286 => require(_exists(tokenId), "ERC721Metadata: URI query for nonexistent token");
2023-03-neotokyo/contracts/s2/NTS2LandDeploy.sol::1304 => require(_exists(tokenId), "ERC721Metadata: URI query for nonexistent token");
2023-03-neotokyo/contracts/s2/NTS2LandDeploy.sol::1315 => require(landMintActive, "Minting is not currently active");
2023-03-neotokyo/contracts/s2/NTS2LandDeploy.sol::1316 => require(whitelistValidated(identityTokenId, spotOnLeaderboard, spotInWhitelist, proof), "That identity cannot claim that land");
2023-03-neotokyo/contracts/s2/NTS2LandDeploy.sol::1317 => require(identityValidated(identityTokenId), "You are not the owner of that identity");
2023-03-neotokyo/contracts/s2/NTS2LandDeploy.sol::1330 => require(address(bytesContract) != address(0), "Land cannot be bought yet");
2023-03-neotokyo/contracts/s2/NTS2LandDeploy.sol::1339 => require(tokenId > 0 && tokenId < 4501, "Token ID invalid");
2023-03-neotokyo/contracts/s2/NTS2LandDeploy.sol::1340 => require(!_exists(tokenId), "That token already exists");
2023-03-neotokyo/contracts/s2/NTS2LandDeploy.sol::1341 => require(_identityClaims[identityId] == 0, "That identity has already claimed");
2023-03-neotokyo/contracts/s2/NTS2LandDeploy.sol::1348 => require(_identityClaims[identityId] == 0, "This identity has minted");
2023-03-neotokyo/contracts/staking/NeoTokyoStaker.sol::1751 => required to deduce the resulting S1 Identity "Credit Yield" strings.
```

## [G-14] Do not calculate constants

Due to how constant variables are implemented (replacements at compile-time), an expression assigned to a constant variable is recomputed each time that the variable is used, which wastes some gas.
Consequences: each usage of a constant costs more gas on each access. Since these are not real constants, they can't be referenced from a real constant environment (e.g. from assembly, or from another library)

```
2023-03-neotokyo/contracts/staking/NeoTokyoStaker.sol::203 => uint256 constant private _BYTES_PER_POINT = 200 * 1e18;
```

## [G-15] Use a more recent version of solidity

Use a solidity version of at least 0.8.0 to get overflow protection without SafeMath
Use a solidity version of at least 0.8.2 to get compiler automatic inlining
Use a solidity version of at least 0.8.3 to get better struct packing and cheaper multiple storage reads
Use a solidity version of at least 0.8.4 to get custom errors, which are cheaper at deployment than revert()/require() strings
Use a solidity version of at least 0.8.10 to have external calls skip contract existence checks if the external call has a return value

```
2023-03-neotokyo/contracts/s1/BYTESContract.sol::5 => pragma solidity ^0.8.0;
2023-03-neotokyo/contracts/s1/NTItems.sol::10 => pragma solidity ^0.8.0;
2023-03-neotokyo/contracts/s1/NTLandDeploy.sol::10 => pragma solidity ^0.8.0;
2023-03-neotokyo/contracts/s1/beckLoot.sol::10 => pragma solidity ^0.8.0;
2023-03-neotokyo/contracts/s1/vaultBox.sol::10 => pragma solidity ^0.8.0;
2023-03-neotokyo/contracts/s2/NTOuterIdentity.sol::10 => pragma solidity ^0.8.0;
2023-03-neotokyo/contracts/s2/NTS2Items.sol::10 => pragma solidity ^0.8.0;
2023-03-neotokyo/contracts/s2/NTS2LandDeploy.sol::10 => pragma solidity ^0.8.0;
```

## [G-16] Prefix increments cheaper than Postfix increments

++i costs less gas than i++, especially when it's used in for-loops (--i/i-- too)
Saves 5 gas PER LOOP

```
2023-03-neotokyo/contracts/s1/BYTESContract.sol::1000 => for (uint256 i = 0; i < bytes(_str).length; i++) {
2023-03-neotokyo/contracts/s1/NTItems.sol::1568 => for (uint16 i = 0; i < proof.length; i++) {
2023-03-neotokyo/contracts/s1/NTItems.sol::1578 => for(uint i = 0; i < _rootHash.length; i++)
2023-03-neotokyo/contracts/s1/NTLandDeploy.sol::1405 => for (uint16 i = 0; i < proof.length; i++) {
2023-03-neotokyo/contracts/s1/NTLandDeploy.sol::1415 => for(uint i = 0; i < _rootHash.length; i++)
2023-03-neotokyo/contracts/s1/beckLoot.sol::1527 => for (uint16 i = 0; i < proof.length; i++) {
2023-03-neotokyo/contracts/s1/beckLoot.sol::1537 => for(uint i = 0; i < _rootHash.length; i++)
2023-03-neotokyo/contracts/s1/vaultBox.sol::1478 => i++;
2023-03-neotokyo/contracts/s1/vaultBox.sol::1491 => for (uint16 i = 0; i < proof.length; i++) {
2023-03-neotokyo/contracts/s1/vaultBox.sol::1501 => for(uint i = 0; i < _rootHash.length; i++)
2023-03-neotokyo/contracts/s2/NTOuterIdentity.sol::1537 => for (uint16 i = 0; i < proof.length; i++) {
2023-03-neotokyo/contracts/s2/NTOuterIdentity.sol::1547 => for(uint i = 0; i < _rootHash.length; i++)
2023-03-neotokyo/contracts/s2/NTS2Items.sol::1385 => for (uint16 i = 0; i < proof.length; i++) {
2023-03-neotokyo/contracts/s2/NTS2Items.sol::1395 => for(uint i = 0; i < _rootHash.length; i++)
2023-03-neotokyo/contracts/s2/NTS2LandDeploy.sol::1358 => for (uint16 i = 0; i < proof.length; i++) {
2023-03-neotokyo/contracts/s2/NTS2LandDeploy.sol::1368 => for(uint i = 0; i < _rootHash.length; i++)
2023-03-neotokyo/contracts/staking/NeoTokyoStaker.sol::728 => unchecked { i++; }
2023-03-neotokyo/contracts/staking/NeoTokyoStaker.sol::743 => unchecked { i++; }
2023-03-neotokyo/contracts/staking/NeoTokyoStaker.sol::1284 => i++;
2023-03-neotokyo/contracts/staking/NeoTokyoStaker.sol::1293 => i++;
2023-03-neotokyo/contracts/staking/NeoTokyoStaker.sol::1383 => unchecked { i++; }
```

## [G-17] Use bytes32 instead of string

Use bytes32 instead of string to save gas whenever possible. String is a dynamic data structure and therefore is more gas consuming then bytes32.

```
2023-03-neotokyo/contracts/s1/NTItems.sol::1272 => string[] private weapon = [
2023-03-neotokyo/contracts/s1/NTItems.sol::1301 => string[] private apparel = [
2023-03-neotokyo/contracts/s1/NTItems.sol::1361 => string[] private vehicle = [
2023-03-neotokyo/contracts/s1/NTItems.sol::1381 => string[] private helm = [
2023-03-neotokyo/contracts/s1/NTLandDeploy.sol::1281 => string[] private location = [
2023-03-neotokyo/contracts/s1/beckLoot.sol::1263 => string[] private class = [
2023-03-neotokyo/contracts/s1/beckLoot.sol::1300 => string[] private gender = [
2023-03-neotokyo/contracts/s1/beckLoot.sol::1305 => string[] private race = [
2023-03-neotokyo/contracts/s1/beckLoot.sol::1317 => string[] private ability = [
2023-03-neotokyo/contracts/s1/beckLoot.sol::1329 => string[] private eyes = [
2023-03-neotokyo/contracts/s1/beckLoot.sol::1348 => string[] private creditYield = [
2023-03-neotokyo/contracts/s1/vaultBox.sol::1268 => string[] private class = [
2023-03-neotokyo/contracts/s1/vaultBox.sol::1305 => string[] private additionalItem = [
2023-03-neotokyo/contracts/s1/vaultBox.sol::1329 => string[] private creditMultiplier = [
2023-03-neotokyo/contracts/s2/NTS2LandDeploy.sol::1270 => string[] private location = [
2023-03-neotokyo/contracts/staking/NeoTokyoStaker.sol::639 => string memory vaultMultiplier = (_vaultId != 0)
2023-03-neotokyo/contracts/staking/NeoTokyoStaker.sol::665 => string memory vaultMultiplier = (_vaultId != 0)
2023-03-neotokyo/contracts/staking/NeoTokyoStaker.sol::939 => string memory citizenCreditYield = getCreditYield(
2023-03-neotokyo/contracts/staking/NeoTokyoStaker.sol::949 => string memory class = IGenericGetter(IDENTITY).getClass(identityId);
```

## [G-18] Splitting require() statements that use && saves gas

Saves 16 gas per instance.
If you're using the Optimizer at 200, instead of using the && operator in a single require statement to check multiple conditions, multiple require statements with 1 condition per require statement should be used to save gas:

```
2023-03-neotokyo/contracts/s1/NTItems.sol::1506 => require(boxTokenId > 0 && boxTokenId < 2501, "That is not a claimable item");
2023-03-neotokyo/contracts/s1/NTItems.sol::1549 => require(tokenId > 0 && tokenId < 2501, "Token ID invalid");
2023-03-neotokyo/contracts/s1/NTLandDeploy.sol::1340 => require(vaultTokenId > 0 && vaultTokenId < 2501, "That vault cannot claim land");
2023-03-neotokyo/contracts/s1/NTLandDeploy.sol::1374 => require(tokenId > 0 && tokenId < 2501, "Token ID invalid");
2023-03-neotokyo/contracts/s1/beckLoot.sol::1489 => require(tokenId > 0 && tokenId < 2001, "Token ID invalid");
2023-03-neotokyo/contracts/s1/beckLoot.sol::1496 => require(tokenId > 0 && tokenId < 2001, "Token ID invalid");
2023-03-neotokyo/contracts/s1/beckLoot.sol::1507 => require(tokenId > 2000 && tokenId < 2251, "Token ID invalid");
2023-03-neotokyo/contracts/s1/beckLoot.sol::1512 => require(tokenId > 2250 && tokenId < 2281, "Token ID invalid");
2023-03-neotokyo/contracts/s1/beckLoot.sol::1517 => require(tokenId > 2280 && tokenId < 2288, "Token ID invalid");
2023-03-neotokyo/contracts/s1/vaultBox.sol::1425 => require(tokenId > 0 && tokenId < 2501, "Token ID invalid");
2023-03-neotokyo/contracts/s1/vaultBox.sol::1442 => require(tokenId > 0 && tokenId < 2501, "Token ID invalid");
2023-03-neotokyo/contracts/s1/vaultBox.sol::1453 => require(tokenId > 0 && tokenId < 2501, "Token ID invalid");
2023-03-neotokyo/contracts/s1/vaultBox.sol::1460 => require(tokenId > 0 && tokenId < 2501, "Token ID invalid");
2023-03-neotokyo/contracts/s2/NTS2Items.sol::1342 => require(identityTokenId > 0 && identityTokenId < 4501, "That is not a claimable item");
2023-03-neotokyo/contracts/s2/NTS2Items.sol::1366 => require(tokenId > 0 && tokenId < 4501, "Token ID invalid");
2023-03-neotokyo/contracts/s2/NTS2LandDeploy.sol::1339 => require(tokenId > 0 && tokenId < 4501, "Token ID invalid");
```

## [G-19] Usage of assert() instead of require()

Between solc 0.4.10 and 0.8.0, require() used REVERT (0xfd) opcode which refunded remaining gas on failure while assert() used INVALID (0xfe) opcode which consumed all the supplied gas.
require() should be used for checking error conditions on inputs and return values while assert() should be used for invariant checking (properly functioning code should never reach a failing assert statement, unless there is a bug in your contract you should fix).

```
2023-03-neotokyo/contracts/s1/BYTESContract.sol::390 => // assert(a == b * c + a % b); // There is no case in which this doesn't hold
```

## [G-20] Public functions not called by the contract should be declared external instead

Contracts are allowed to override their parents' functions and change the visibility from external to public and can save gas by doing so.

```
2023-03-neotokyo/contracts/s1/BYTESContract.sol::475 => function renounceOwnership() public virtual onlyOwner {
2023-03-neotokyo/contracts/s1/BYTESContract.sol::483 => function transferOwnership(address newOwner) public virtual onlyOwner {
2023-03-neotokyo/contracts/s1/BYTESContract.sol::553 => function name() public view returns (string memory) {
2023-03-neotokyo/contracts/s1/BYTESContract.sol::561 => function symbol() public view returns (string memory) {
2023-03-neotokyo/contracts/s1/BYTESContract.sol::578 => function decimals() public view returns (uint8) {
2023-03-neotokyo/contracts/s1/BYTESContract.sol::658 => function increaseAllowance(address spender, uint256 addedValue) public virtual returns (bool) {
2023-03-neotokyo/contracts/s1/BYTESContract.sol::677 => function decreaseAllowance(address spender, uint256 subtractedValue) public virtual returns (bool) {
2023-03-neotokyo/contracts/s1/BYTESContract.sol::823 => function openVaultBox(uint256 identityTokenId, uint256 vaultBoxTokenId) public
2023-03-neotokyo/contracts/s1/BYTESContract.sol::844 => function hasVaultBoxBeenOpened(uint256 tokenId) public view returns(bool)
2023-03-neotokyo/contracts/s1/BYTESContract.sol::854 => function hasIdentityOpenedABox(uint256 tokenId) public view returns(bool)
2023-03-neotokyo/contracts/s1/BYTESContract.sol::864 => function updateMaxRewardableTokens(uint256 _amount) public onlyOwner
2023-03-neotokyo/contracts/s1/BYTESContract.sol::869 => function addAdminContractAddress(address _address) public onlyOwner
2023-03-neotokyo/contracts/s1/BYTESContract.sol::874 => function removeAdminContactAddress(address _address) public onlyOwner
2023-03-neotokyo/contracts/s1/BYTESContract.sol::900 => function setIdentityContract(address _address) public onlyOwner {
2023-03-neotokyo/contracts/s1/BYTESContract.sol::904 => function setBoughtIdentityContract(address _address) public onlyOwner {
2023-03-neotokyo/contracts/s1/BYTESContract.sol::908 => function setVaultBoxContract(address _address) public onlyOwner {
2023-03-neotokyo/contracts/s1/BYTESContract.sol::912 => function setCitizenContract(address _address) public onlyOwner {
2023-03-neotokyo/contracts/s1/NTCitizenDeploy.sol::328 => function renounceOwnership() public virtual onlyOwner {
2023-03-neotokyo/contracts/s1/NTCitizenDeploy.sol::336 => function transferOwnership(address newOwner) public virtual onlyOwner {
2023-03-neotokyo/contracts/s1/NTCitizenDeploy.sol::1310 => function getIdentityIdOfTokenId(uint256 citizenId) public view returns (uint256) {
2023-03-neotokyo/contracts/s1/NTCitizenDeploy.sol::1313 => function getVaultIdOfTokenId(uint256 citizenId) public view returns (uint256) {
2023-03-neotokyo/contracts/s1/NTCitizenDeploy.sol::1316 => function getItemCacheIdOfTokenId(uint256 citizenId) public view returns (uint256) {
2023-03-neotokyo/contracts/s1/NTCitizenDeploy.sol::1319 => function getLandDeedIdOfTokenId(uint256 citizenId) public view returns (uint256) {
2023-03-neotokyo/contracts/s1/NTCitizenDeploy.sol::1322 => function getSpecialMessageOfTokenId(uint256 citizenId) public view returns (string memory) {
2023-03-neotokyo/contracts/s1/NTCitizenDeploy.sol::1325 => function getRewardRateOfTokenId(uint256 citizenId) public view returns (uint256) {
2023-03-neotokyo/contracts/s1/NTCitizenDeploy.sol::1328 => function getGenderOfTokenId(uint256 citizenId) public view returns (bool) {
2023-03-neotokyo/contracts/s1/NTCitizenDeploy.sol::1361 => function getRewardRate(address _user) public view returns(uint256) {
2023-03-neotokyo/contracts/s1/NTCitizenDeploy.sol::1370 => function getRewardsRateForTokenId(uint256 tokenId) public view returns(uint256){
2023-03-neotokyo/contracts/s1/NTCitizenDeploy.sol::1384 => function getCurrentOrFinalTime() public view returns(uint256) {
2023-03-neotokyo/contracts/s1/NTCitizenDeploy.sol::1396 => function reduceRewards(uint256, address) public {}
2023-03-neotokyo/contracts/s1/NTCitizenDeploy.sol::1399 => function increaseRewards(uint256, address) public {}
2023-03-neotokyo/contracts/s1/NTCitizenDeploy.sol::1410 => function createCitizen(uint256 identityId, uint256 vaultId, uint256 itemCacheId, uint256 landDeedId, bool genderFemale, string memory specialMessage) public nonReentrant {
2023-03-neotokyo/contracts/s1/NTCitizenDeploy.sol::1476 => function disassembleCitizen(uint256 citizenId)public nonReentrant {
2023-03-neotokyo/contracts/s1/NTCitizenDeploy.sol::1514 => function changeGender(uint256 tokenId) public nonReentrant {
2023-03-neotokyo/contracts/s1/NTCitizenDeploy.sol::1523 => function changeSpecialMessage(uint256 tokenId, string memory _message) public nonReentrant {
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
2023-03-neotokyo/contracts/s1/NTCitizenDeploy.sol::1611 => function mintIdentity() public
2023-03-neotokyo/contracts/s1/NTItems.sol::308 => function renounceOwnership() public virtual onlyOwner {
2023-03-neotokyo/contracts/s1/NTItems.sol::316 => function transferOwnership(address newOwner) public virtual onlyOwner {
2023-03-neotokyo/contracts/s1/NTItems.sol::1484 => function getTokenClaimedByBoxTokenId(uint256 boxTokenId) public view returns (uint256) {
2023-03-neotokyo/contracts/s1/NTItems.sol::1488 => function getClaimantBoxIdByTokenId(uint256 tokenId) public view returns (uint256) {
2023-03-neotokyo/contracts/s1/NTItems.sol::1504 => function boxClaim(uint256 boxTokenId, uint256 spotOnLeaderboard, uint256 spotInWhitelist, bytes32[] memory proof) public nonReentrant {
2023-03-neotokyo/contracts/s1/NTItems.sol::1538 => function buyItems() public nonReentrant
2023-03-neotokyo/contracts/s1/NTItems.sol::1548 => function emergencyClaim(uint256 boxId, uint256 tokenId) public onlyOwner {
2023-03-neotokyo/contracts/s1/NTItems.sol::1589 => function setItemMintActive() public onlyOwner {
2023-03-neotokyo/contracts/s1/NTItems.sol::1593 => function setItemCost(uint256 _cost) public onlyOwner {
2023-03-neotokyo/contracts/s1/NTItems.sol::1597 => function setContract(address contractAddress) public onlyOwner {
2023-03-neotokyo/contracts/s1/NTItems.sol::1601 => function setBoxAddress(address contractAddress) public onlyOwner {
2023-03-neotokyo/contracts/s1/NTItems.sol::1605 => function setBytesAddress(address contractAddress) public onlyOwner {
2023-03-neotokyo/contracts/s1/NTLandDeploy.sol::308 => function renounceOwnership() public virtual onlyOwner {
2023-03-neotokyo/contracts/s1/NTLandDeploy.sol::316 => function transferOwnership(address newOwner) public virtual onlyOwner {
2023-03-neotokyo/contracts/s1/NTLandDeploy.sol::1307 => function getTokenClaimedByVaultTokenId(uint256 vaultTokenId) public view returns (uint256) {
2023-03-neotokyo/contracts/s1/NTLandDeploy.sol::1310 => function getTokenClaimedByIdentityTokenId(uint256 identityTokenId) public view returns (uint256) {
2023-03-neotokyo/contracts/s1/NTLandDeploy.sol::1313 => function getTokenClaimedByItemCacheTokenId(uint256 itemCacheTokenId) public view returns (uint256) {
2023-03-neotokyo/contracts/s1/NTLandDeploy.sol::1317 => function getClaimantVaultIdByTokenId(uint256 tokenId) public view returns (uint256) {
2023-03-neotokyo/contracts/s1/NTLandDeploy.sol::1320 => function getClaimantIdentityIdByTokenId(uint256 tokenId) public view returns (uint256) {
2023-03-neotokyo/contracts/s1/NTLandDeploy.sol::1323 => function getClaimantItemCacheIdByTokenId(uint256 tokenId) public view returns (uint256) {
2023-03-neotokyo/contracts/s1/NTLandDeploy.sol::1338 => function landClaim(uint256 vaultTokenId, uint256 identityTokenId, uint256 itemCacheTokenId, uint256 spotOnLeaderboard, uint256 spotInWhitelist, bytes32[] memory proof) public nonReentrant {
2023-03-neotokyo/contracts/s1/NTLandDeploy.sol::1363 => function buyLand() public nonReentrant
2023-03-neotokyo/contracts/s1/NTLandDeploy.sol::1373 => function emergencyClaim(uint256 vaultId, uint256 tokenId) public onlyOwner {
2023-03-neotokyo/contracts/s1/NTLandDeploy.sol::1426 => function setLandMintActive() public onlyOwner {
2023-03-neotokyo/contracts/s1/NTLandDeploy.sol::1430 => function setLandCost(uint256 _cost) public onlyOwner {
2023-03-neotokyo/contracts/s1/NTLandDeploy.sol::1434 => function setContract(address contractAddress) public onlyOwner {
2023-03-neotokyo/contracts/s1/NTLandDeploy.sol::1438 => function setItemContract(address contractAddress) public onlyOwner {
2023-03-neotokyo/contracts/s1/NTLandDeploy.sol::1442 => function setVaultAddress(address contractAddress) public onlyOwner {
2023-03-neotokyo/contracts/s1/NTLandDeploy.sol::1446 => function setBytesAddress(address contractAddress) public onlyOwner {
2023-03-neotokyo/contracts/s1/beckLoot.sol::305 => function renounceOwnership() public virtual onlyOwner {
2023-03-neotokyo/contracts/s1/beckLoot.sol::313 => function transferOwnership(address newOwner) public virtual onlyOwner {
2023-03-neotokyo/contracts/s1/beckLoot.sol::1488 => function claim(uint256 tokenId) public nonReentrant {
2023-03-neotokyo/contracts/s1/beckLoot.sol::1494 => function whitelistClaim(uint256 tokenId, uint256 spotInWhitelist, bytes32[] memory proof) public nonReentrant {
2023-03-neotokyo/contracts/s1/beckLoot.sol::1506 => function ownerClaim(uint256 tokenId) public nonReentrant onlyOwner {
2023-03-neotokyo/contracts/s1/beckLoot.sol::1511 => function riddleClaim(address _to, uint256 tokenId) public nonReentrant onlyOwner {
2023-03-neotokyo/contracts/s1/beckLoot.sol::1516 => function handClaim(address _to, uint256 tokenId) public nonReentrant onlyOwner {
2023-03-neotokyo/contracts/s1/beckLoot.sol::1548 => function setMintContract(address contractAddress) public onlyOwner {
2023-03-neotokyo/contracts/s1/beckLoot.sol::1552 => function setRareMintContract(address contractAddress) public onlyOwner {
2023-03-neotokyo/contracts/s1/beckLoot.sol::1556 => function setHandMintContract(address contractAddress) public onlyOwner {
2023-03-neotokyo/contracts/s1/vaultBox.sol::305 => function renounceOwnership() public virtual onlyOwner {
2023-03-neotokyo/contracts/s1/vaultBox.sol::313 => function transferOwnership(address newOwner) public virtual onlyOwner {
2023-03-neotokyo/contracts/s1/vaultBox.sol::1395 => function getTokenClaimedByIdentityTokenId(uint256 identityTokenId) public view returns (uint256) {
2023-03-neotokyo/contracts/s1/vaultBox.sol::1399 => function getClaimantIdentityIdByTokenId(uint256 tokenId) public view returns (uint256) {
2023-03-neotokyo/contracts/s1/vaultBox.sol::1424 => function holderClaim(uint256 tokenId) public nonReentrant {
2023-03-neotokyo/contracts/s1/vaultBox.sol::1440 => function whitelistClaim(uint256 tokenId, uint256 spotInWhitelist, bytes32[] memory proof) public nonReentrant {
2023-03-neotokyo/contracts/s1/vaultBox.sol::1452 => function claim(uint256 tokenId) public nonReentrant {
2023-03-neotokyo/contracts/s1/vaultBox.sol::1459 => function ownerClaim(uint256 tokenId) public nonReentrant onlyOwner {
2023-03-neotokyo/contracts/s1/vaultBox.sol::1512 => function setContract(address contractAddress) public onlyOwner {
2023-03-neotokyo/contracts/s1/vaultBox.sol::1516 => function setIdAddress(address contractAddress) public onlyOwner {
2023-03-neotokyo/contracts/s2/NTOuterCitizenDeploy.sol::324 => function renounceOwnership() public virtual onlyOwner {
2023-03-neotokyo/contracts/s2/NTOuterCitizenDeploy.sol::332 => function transferOwnership(address newOwner) public virtual onlyOwner {
2023-03-neotokyo/contracts/s2/NTOuterCitizenDeploy.sol::1307 => function getIdentityIdOfTokenId(uint256 citizenId) public view returns (uint256) {
2023-03-neotokyo/contracts/s2/NTOuterCitizenDeploy.sol::1310 => function getItemCacheIdOfTokenId(uint256 citizenId) public view returns (uint256) {
2023-03-neotokyo/contracts/s2/NTOuterCitizenDeploy.sol::1313 => function getLandDeedIdOfTokenId(uint256 citizenId) public view returns (uint256) {
2023-03-neotokyo/contracts/s2/NTOuterCitizenDeploy.sol::1316 => function getSpecialMessageOfTokenId(uint256 citizenId) public view returns (string memory) {
2023-03-neotokyo/contracts/s2/NTOuterCitizenDeploy.sol::1319 => function getGenderOfTokenId(uint256 citizenId) public view returns (bool) {
2023-03-neotokyo/contracts/s2/NTOuterCitizenDeploy.sol::1352 => function createCitizen(uint256 identityId, uint256 itemCacheId, uint256 landDeedId, bool genderFemale, string memory specialMessage) public nonReentrant {
2023-03-neotokyo/contracts/s2/NTOuterCitizenDeploy.sol::1401 => function disassembleCitizen(uint256 citizenId)public nonReentrant {
2023-03-neotokyo/contracts/s2/NTOuterCitizenDeploy.sol::1431 => function changeGender(uint256 tokenId) public nonReentrant {
2023-03-neotokyo/contracts/s2/NTOuterCitizenDeploy.sol::1440 => function changeSpecialMessage(uint256 tokenId, string memory _message) public nonReentrant {
2023-03-neotokyo/contracts/s2/NTOuterCitizenDeploy.sol::1479 => function setFemaleActive() public onlyOwner {
2023-03-neotokyo/contracts/s2/NTOuterCitizenDeploy.sol::1483 => function setCitizenMintActive() public onlyOwner {
2023-03-neotokyo/contracts/s2/NTOuterCitizenDeploy.sol::1487 => function setBoughtIdentitiesActive() public onlyOwner {
2023-03-neotokyo/contracts/s2/NTOuterCitizenDeploy.sol::1495 => function setIdentityAddress(address contractAddress) public onlyOwner {
2023-03-neotokyo/contracts/s2/NTOuterCitizenDeploy.sol::1499 => function setLandContract(address contractAddress) public onlyOwner {
2023-03-neotokyo/contracts/s2/NTOuterCitizenDeploy.sol::1503 => function setItemContract(address contractAddress) public onlyOwner {
2023-03-neotokyo/contracts/s2/NTOuterCitizenDeploy.sol::1507 => function setBytesAddress(address contractAddress) public onlyOwner {
2023-03-neotokyo/contracts/s2/NTOuterCitizenDeploy.sol::1511 => function setCitizenMintContract(address contractAddress) public onlyOwner {
2023-03-neotokyo/contracts/s2/NTOuterCitizenDeploy.sol::1515 => function setCitizenAlternateMintContract(address contractAddress) public onlyOwner {
2023-03-neotokyo/contracts/s2/NTOuterCitizenDeploy.sol::1519 => function setMintedIdentityCost(uint256 _cost) public onlyOwner {
2023-03-neotokyo/contracts/s2/NTOuterCitizenDeploy.sol::1523 => function mintIdentity() public
2023-03-neotokyo/contracts/s2/NTOuterIdentity.sol::309 => function renounceOwnership() public virtual onlyOwner {
2023-03-neotokyo/contracts/s2/NTOuterIdentity.sol::317 => function transferOwnership(address newOwner) public virtual onlyOwner {
2023-03-neotokyo/contracts/s2/NTOuterIdentity.sol::1320 => function getMostBytesBurned() public view returns(uint256)
2023-03-neotokyo/contracts/s2/NTOuterIdentity.sol::1473 => function reserveIdentity() public nonReentrant payable {
2023-03-neotokyo/contracts/s2/NTOuterIdentity.sol::1497 => function burnBytes(uint256 amount) public nonReentrant
2023-03-neotokyo/contracts/s2/NTOuterIdentity.sol::1514 => function whitelistClaim(uint256 tokenId, uint256 spotInWhitelist, bytes32[] memory proof) public nonReentrant {
2023-03-neotokyo/contracts/s2/NTOuterIdentity.sol::1526 => function ownerClaim(uint256 tokenId) public nonReentrant onlyOwner {
2023-03-neotokyo/contracts/s2/NTOuterIdentity.sol::1558 => function setOuterIdentityMintContract(address contractAddress) public onlyOwner {
2023-03-neotokyo/contracts/s2/NTS2Items.sol::308 => function renounceOwnership() public virtual onlyOwner {
2023-03-neotokyo/contracts/s2/NTS2Items.sol::316 => function transferOwnership(address newOwner) public virtual onlyOwner {
2023-03-neotokyo/contracts/s2/NTS2Items.sol::1316 => function getTokenClaimedByIdentityTokenId(uint256 identityTokenId) public view returns (uint256) {
2023-03-neotokyo/contracts/s2/NTS2Items.sol::1320 => function getClaimantIdentityIdByTokenId(uint256 tokenId) public view returns (uint256) {
2023-03-neotokyo/contracts/s2/NTS2Items.sol::1340 => function identityClaim(uint256 identityTokenId, uint256 spotOnLeaderboard, uint256 spotInWhitelist, bytes32[] memory proof) public nonReentrant {
2023-03-neotokyo/contracts/s2/NTS2Items.sol::1355 => function buyItems() public nonReentrant
2023-03-neotokyo/contracts/s2/NTS2Items.sol::1365 => function emergencyClaim(uint256 identityId, uint256 tokenId) public onlyOwner {
2023-03-neotokyo/contracts/s2/NTS2Items.sol::1406 => function setItemMintActive() public onlyOwner {
2023-03-neotokyo/contracts/s2/NTS2Items.sol::1410 => function setItemCost(uint256 _cost) public onlyOwner {
2023-03-neotokyo/contracts/s2/NTS2Items.sol::1414 => function setContract(address contractAddress) public onlyOwner {
2023-03-neotokyo/contracts/s2/NTS2Items.sol::1418 => function setIdentityAddress(address contractAddress) public onlyOwner {
2023-03-neotokyo/contracts/s2/NTS2Items.sol::1422 => function setBytesAddress(address contractAddress) public onlyOwner {
2023-03-neotokyo/contracts/s2/NTS2LandDeploy.sol::308 => function renounceOwnership() public virtual onlyOwner {
2023-03-neotokyo/contracts/s2/NTS2LandDeploy.sol::316 => function transferOwnership(address newOwner) public virtual onlyOwner {
2023-03-neotokyo/contracts/s2/NTS2LandDeploy.sol::1295 => function getTokenClaimedByIdentityTokenId(uint256 identityTokenId) public view returns (uint256) {
2023-03-neotokyo/contracts/s2/NTS2LandDeploy.sol::1299 => function getClaimantIdentityIdByTokenId(uint256 tokenId) public view returns (uint256) {
2023-03-neotokyo/contracts/s2/NTS2LandDeploy.sol::1314 => function landClaim(uint256 identityTokenId, uint256 spotOnLeaderboard, uint256 spotInWhitelist, bytes32[] memory proof) public nonReentrant {
2023-03-neotokyo/contracts/s2/NTS2LandDeploy.sol::1328 => function buyLand() public nonReentrant
2023-03-neotokyo/contracts/s2/NTS2LandDeploy.sol::1338 => function emergencyClaim(uint256 identityId, uint256 tokenId) public onlyOwner {
2023-03-neotokyo/contracts/s2/NTS2LandDeploy.sol::1379 => function setLandMintActive() public onlyOwner {
2023-03-neotokyo/contracts/s2/NTS2LandDeploy.sol::1383 => function setLandCost(uint256 _cost) public onlyOwner {
2023-03-neotokyo/contracts/s2/NTS2LandDeploy.sol::1387 => function setContract(address contractAddress) public onlyOwner {
2023-03-neotokyo/contracts/s2/NTS2LandDeploy.sol::1391 => function setIdentityContract(address contractAddress) public onlyOwner {
2023-03-neotokyo/contracts/s2/NTS2LandDeploy.sol::1395 => function setBytesAddress(address contractAddress) public onlyOwner {
```

## [G-21] Not using the named return variables when a function returns, wastes deployment gas

It is not necessary to have both a named return and a return statement.

```
2023-03-neotokyo/contracts/s1/BYTESContract.sol::185 => function _msgSender() internal view virtual returns (address payable) {
2023-03-neotokyo/contracts/s1/BYTESContract.sol::999 => function strToUint(string memory _str) public pure returns(uint256 res, bool err) {
2023-03-neotokyo/contracts/s1/NTCitizenDeploy.sol::276 => function _msgData() internal view virtual returns (bytes calldata) {
2023-03-neotokyo/contracts/s1/NTCitizenDeploy.sol::1053 => try IERC721Receiver(to).onERC721Received(_msgSender(), from, tokenId, _data) returns (bytes4 retval) {
2023-03-neotokyo/contracts/s1/NTItems.sol::256 => function _msgData() internal view virtual returns (bytes calldata) {
2023-03-neotokyo/contracts/s1/NTItems.sol::1033 => try IERC721Receiver(to).onERC721Received(_msgSender(), from, tokenId, _data) returns (bytes4 retval) {
2023-03-neotokyo/contracts/s1/NTLandDeploy.sol::256 => function _msgData() internal view virtual returns (bytes calldata) {
2023-03-neotokyo/contracts/s1/NTLandDeploy.sol::1033 => try IERC721Receiver(to).onERC721Received(_msgSender(), from, tokenId, _data) returns (bytes4 retval) {
2023-03-neotokyo/contracts/s1/beckLoot.sol::253 => function _msgData() internal view virtual returns (bytes calldata) {
2023-03-neotokyo/contracts/s1/beckLoot.sol::1030 => try IERC721Receiver(to).onERC721Received(_msgSender(), from, tokenId, _data) returns (bytes4 retval) {
2023-03-neotokyo/contracts/s1/vaultBox.sol::253 => function _msgData() internal view virtual returns (bytes calldata) {
2023-03-neotokyo/contracts/s1/vaultBox.sol::1030 => try IERC721Receiver(to).onERC721Received(_msgSender(), from, tokenId, _data) returns (bytes4 retval) {
2023-03-neotokyo/contracts/s2/NTOuterCitizenDeploy.sol::272 => function _msgData() internal view virtual returns (bytes calldata) {
2023-03-neotokyo/contracts/s2/NTOuterCitizenDeploy.sol::1049 => try IERC721Receiver(to).onERC721Received(_msgSender(), from, tokenId, _data) returns (bytes4 retval) {
2023-03-neotokyo/contracts/s2/NTOuterIdentity.sol::257 => function _msgData() internal view virtual returns (bytes calldata) {
2023-03-neotokyo/contracts/s2/NTOuterIdentity.sol::1034 => try IERC721Receiver(to).onERC721Received(_msgSender(), from, tokenId, _data) returns (bytes4 retval) {
2023-03-neotokyo/contracts/s2/NTS2Items.sol::256 => function _msgData() internal view virtual returns (bytes calldata) {
2023-03-neotokyo/contracts/s2/NTS2Items.sol::1033 => try IERC721Receiver(to).onERC721Received(_msgSender(), from, tokenId, _data) returns (bytes4 retval) {
2023-03-neotokyo/contracts/s2/NTS2LandDeploy.sol::256 => function _msgData() internal view virtual returns (bytes calldata) {
2023-03-neotokyo/contracts/s2/NTS2LandDeploy.sol::1033 => try IERC721Receiver(to).onERC721Received(_msgSender(), from, tokenId, _data) returns (bytes4 retval) {
```

## [G-22] Multiple address mappings can be combined into a single mapping of an address to a struct, where appropriate

Saves a storage slot for the mapping. Depending on the circumstances and sizes of types, can avoid a Gsset (20000 gas) per mapping combined. Reads and subsequent writes can also be cheaper when a function requires both values and they both fit in the same storage slot. Finally, if both fields are accessed in the same function, can save ~42 gas per access due to not having to recalculate the key's keccak256 hash (Gkeccak256 - 30 gas) and that calculation's associated stack operations.

```
2023-03-neotokyo/contracts/s1/BYTESContract.sol::803 => mapping(address => uint256) public rewards;
2023-03-neotokyo/contracts/s1/BYTESContract.sol::804 => mapping(address => uint256) public lastUpdate;
2023-03-neotokyo/contracts/s1/BYTESContract.sol::805 => mapping(address => bool) public adminContracts;
2023-03-neotokyo/contracts/s1/NTCitizenDeploy.sol::709 => mapping(address => uint256) private _balances;
2023-03-neotokyo/contracts/s1/NTCitizenDeploy.sol::715 => mapping(address => mapping(address => bool)) private _operatorApprovals;
2023-03-neotokyo/contracts/s1/NTCitizenDeploy.sol::1123 => mapping(address => mapping(uint256 => uint256)) private _ownedTokens;
2023-03-neotokyo/contracts/s1/NTItems.sol::689 => mapping(address => uint256) private _balances;
2023-03-neotokyo/contracts/s1/NTItems.sol::695 => mapping(address => mapping(address => bool)) private _operatorApprovals;
2023-03-neotokyo/contracts/s1/NTItems.sol::1103 => mapping(address => mapping(uint256 => uint256)) private _ownedTokens;
2023-03-neotokyo/contracts/s1/NTLandDeploy.sol::689 => mapping(address => uint256) private _balances;
2023-03-neotokyo/contracts/s1/NTLandDeploy.sol::695 => mapping(address => mapping(address => bool)) private _operatorApprovals;
2023-03-neotokyo/contracts/s1/NTLandDeploy.sol::1103 => mapping(address => mapping(uint256 => uint256)) private _ownedTokens;
2023-03-neotokyo/contracts/s1/beckLoot.sol::686 => mapping(address => uint256) private _balances;
2023-03-neotokyo/contracts/s1/beckLoot.sol::692 => mapping(address => mapping(address => bool)) private _operatorApprovals;
2023-03-neotokyo/contracts/s1/beckLoot.sol::1100 => mapping(address => mapping(uint256 => uint256)) private _ownedTokens;
2023-03-neotokyo/contracts/s1/beckLoot.sol::1261 => mapping(address => uint8) private _previousMinters;
2023-03-neotokyo/contracts/s1/vaultBox.sol::686 => mapping(address => uint256) private _balances;
2023-03-neotokyo/contracts/s1/vaultBox.sol::692 => mapping(address => mapping(address => bool)) private _operatorApprovals;
2023-03-neotokyo/contracts/s1/vaultBox.sol::1100 => mapping(address => mapping(uint256 => uint256)) private _ownedTokens;
2023-03-neotokyo/contracts/s1/vaultBox.sol::1260 => mapping(address => uint256) private _whitelistMinters;
2023-03-neotokyo/contracts/s2/NTOuterCitizenDeploy.sol::705 => mapping(address => uint256) private _balances;
2023-03-neotokyo/contracts/s2/NTOuterCitizenDeploy.sol::711 => mapping(address => mapping(address => bool)) private _operatorApprovals;
2023-03-neotokyo/contracts/s2/NTOuterCitizenDeploy.sol::1119 => mapping(address => mapping(uint256 => uint256)) private _ownedTokens;
2023-03-neotokyo/contracts/s2/NTOuterCitizenDeploy.sol::1271 => mapping(address => OwnableDelegateProxy) public proxies;
2023-03-neotokyo/contracts/s2/NTOuterIdentity.sol::690 => mapping(address => uint256) private _balances;
2023-03-neotokyo/contracts/s2/NTOuterIdentity.sol::696 => mapping(address => mapping(address => bool)) private _operatorApprovals;
2023-03-neotokyo/contracts/s2/NTOuterIdentity.sol::1104 => mapping(address => mapping(uint256 => uint256)) private _ownedTokens;
2023-03-neotokyo/contracts/s2/NTOuterIdentity.sol::1277 => mapping(address => uint8) private _previousMinters;
2023-03-neotokyo/contracts/s2/NTOuterIdentity.sol::1279 => mapping(address => uint256) public bytesBurned;
2023-03-neotokyo/contracts/s2/NTOuterIdentity.sol::1281 => mapping(address => bool) public addressHasReserved;
2023-03-neotokyo/contracts/s2/NTS2Items.sol::689 => mapping(address => uint256) private _balances;
2023-03-neotokyo/contracts/s2/NTS2Items.sol::695 => mapping(address => mapping(address => bool)) private _operatorApprovals;
2023-03-neotokyo/contracts/s2/NTS2Items.sol::1103 => mapping(address => mapping(uint256 => uint256)) private _ownedTokens;
2023-03-neotokyo/contracts/s2/NTS2LandDeploy.sol::689 => mapping(address => uint256) private _balances;
2023-03-neotokyo/contracts/s2/NTS2LandDeploy.sol::695 => mapping(address => mapping(address => bool)) private _operatorApprovals;
2023-03-neotokyo/contracts/s2/NTS2LandDeploy.sol::1103 => mapping(address => mapping(uint256 => uint256)) private _ownedTokens;
```

## [G-23] internal functions only called once can be inlined to save gas

Not inlining costs 20 to 40 gas because of two extra JUMP instructions and additional stack operations needed for function calls.

```
2023-03-neotokyo/contracts/s1/BYTESContract.sol::55 => function isContract(address account) internal view returns (bool) {
2023-03-neotokyo/contracts/s1/BYTESContract.sol::82 => function sendValue(address payable recipient, uint256 amount) internal {
2023-03-neotokyo/contracts/s1/BYTESContract.sol::189 => function _msgData() internal view virtual returns (bytes memory) {
2023-03-neotokyo/contracts/s1/BYTESContract.sol::737 => function _burn(address account, uint256 amount) internal virtual {
2023-03-neotokyo/contracts/s1/BYTESContract.sol::775 => function _setupDecimals(uint8 decimals_) internal {
2023-03-neotokyo/contracts/s1/BYTESContract.sol::879 => function validateIdentity(uint256 tokenId) internal view returns(bool)
2023-03-neotokyo/contracts/s1/NTCitizenDeploy.sol::276 => function _msgData() internal view virtual returns (bytes calldata) {
2023-03-neotokyo/contracts/s1/NTCitizenDeploy.sol::505 => function sendValue(address payable recipient, uint256 amount) internal {
2023-03-neotokyo/contracts/s1/NTCitizenDeploy.sol::781 => function _baseURI() internal view virtual returns (string memory) {
2023-03-neotokyo/contracts/s1/NTCitizenDeploy.sol::959 => function _mint(address to, uint256 tokenId) internal virtual {
2023-03-neotokyo/contracts/s1/NTCitizenDeploy.sol::981 => function _burn(uint256 tokenId) internal virtual {
2023-03-neotokyo/contracts/s1/NTCitizenDeploy.sol::1375 => function rewardRateByTokenId(uint256 tokenId) internal view returns(uint256){
2023-03-neotokyo/contracts/s1/NTCitizenDeploy.sol::1405 => function calculateCitizenReward(uint256 identityId, uint256 vaultId) internal view returns(uint256){
2023-03-neotokyo/contracts/s1/NTCitizenDeploy.sol::1539 => function vaultValidated(uint256 vaultId) internal view returns (bool) {
2023-03-neotokyo/contracts/s1/NTCitizenDeploy.sol::1544 => function identityValidated(uint256 identityId) internal view returns (bool) {
2023-03-neotokyo/contracts/s1/NTCitizenDeploy.sol::1557 => function itemCacheValidated(uint256 itemCacheId) internal view returns (bool) {
2023-03-neotokyo/contracts/s1/NTCitizenDeploy.sol::1562 => function landDeedValidated(uint256 landDeedId) internal view returns (bool) {
2023-03-neotokyo/contracts/s1/NTItems.sol::256 => function _msgData() internal view virtual returns (bytes calldata) {
2023-03-neotokyo/contracts/s1/NTItems.sol::485 => function sendValue(address payable recipient, uint256 amount) internal {
2023-03-neotokyo/contracts/s1/NTItems.sol::761 => function _baseURI() internal view virtual returns (string memory) {
2023-03-neotokyo/contracts/s1/NTItems.sol::939 => function _mint(address to, uint256 tokenId) internal virtual {
2023-03-neotokyo/contracts/s1/NTItems.sol::961 => function _burn(uint256 tokenId) internal virtual {
2023-03-neotokyo/contracts/s1/NTItems.sol::1524 => function specialItemCheck(uint256 spotOnLeaderboard) internal
2023-03-neotokyo/contracts/s1/NTItems.sol::1557 => function boxValidated(uint256 boxId) internal view returns (bool) {
2023-03-neotokyo/contracts/s1/NTItems.sol::1563 => function whitelistValidated(string memory vaultTokenId, uint256 leaderboardSpot,uint256 index, bytes32[] memory proof) internal view returns (bool) {
2023-03-neotokyo/contracts/s1/NTLandDeploy.sol::256 => function _msgData() internal view virtual returns (bytes calldata) {
2023-03-neotokyo/contracts/s1/NTLandDeploy.sol::485 => function sendValue(address payable recipient, uint256 amount) internal {
2023-03-neotokyo/contracts/s1/NTLandDeploy.sol::761 => function _baseURI() internal view virtual returns (string memory) {
2023-03-neotokyo/contracts/s1/NTLandDeploy.sol::939 => function _mint(address to, uint256 tokenId) internal virtual {
2023-03-neotokyo/contracts/s1/NTLandDeploy.sol::961 => function _burn(uint256 tokenId) internal virtual {
2023-03-neotokyo/contracts/s1/NTLandDeploy.sol::1382 => function vaultValidated(uint256 vaultId) internal view returns (bool) {
2023-03-neotokyo/contracts/s1/NTLandDeploy.sol::1388 => function identityValidated(uint256 identityId) internal view returns (bool) {
2023-03-neotokyo/contracts/s1/NTLandDeploy.sol::1394 => function itemCacheValidated(uint256 itemCacheId) internal view returns (bool) {
2023-03-neotokyo/contracts/s1/NTLandDeploy.sol::1400 => function whitelistValidated(string memory vaultTokenId, uint256 leaderboardSpot,uint256 index, bytes32[] memory proof) internal view returns (bool) {
2023-03-neotokyo/contracts/s1/beckLoot.sol::253 => function _msgData() internal view virtual returns (bytes calldata) {
2023-03-neotokyo/contracts/s1/beckLoot.sol::482 => function sendValue(address payable recipient, uint256 amount) internal {
2023-03-neotokyo/contracts/s1/beckLoot.sol::758 => function _baseURI() internal view virtual returns (string memory) {
2023-03-neotokyo/contracts/s1/beckLoot.sol::936 => function _mint(address to, uint256 tokenId) internal virtual {
2023-03-neotokyo/contracts/s1/beckLoot.sol::958 => function _burn(uint256 tokenId) internal virtual {
2023-03-neotokyo/contracts/s1/beckLoot.sol::1521 => function whitelistValidated(address wallet, uint256 index, bytes32[] memory proof) internal view returns (bool) {
2023-03-neotokyo/contracts/s1/vaultBox.sol::253 => function _msgData() internal view virtual returns (bytes calldata) {
2023-03-neotokyo/contracts/s1/vaultBox.sol::482 => function sendValue(address payable recipient, uint256 amount) internal {
2023-03-neotokyo/contracts/s1/vaultBox.sol::758 => function _baseURI() internal view virtual returns (string memory) {
2023-03-neotokyo/contracts/s1/vaultBox.sol::936 => function _mint(address to, uint256 tokenId) internal virtual {
2023-03-neotokyo/contracts/s1/vaultBox.sol::958 => function _burn(uint256 tokenId) internal virtual {
2023-03-neotokyo/contracts/s1/vaultBox.sol::1464 => function holderValidated(address requester) internal view returns (uint256) {
2023-03-neotokyo/contracts/s1/vaultBox.sol::1485 => function whitelistValidated(address wallet, uint256 index, bytes32[] memory proof) internal view returns (bool) {
2023-03-neotokyo/contracts/s2/NTOuterCitizenDeploy.sol::272 => function _msgData() internal view virtual returns (bytes calldata) {
2023-03-neotokyo/contracts/s2/NTOuterCitizenDeploy.sol::501 => function sendValue(address payable recipient, uint256 amount) internal {
2023-03-neotokyo/contracts/s2/NTOuterCitizenDeploy.sol::777 => function _baseURI() internal view virtual returns (string memory) {
2023-03-neotokyo/contracts/s2/NTOuterCitizenDeploy.sol::955 => function _mint(address to, uint256 tokenId) internal virtual {
2023-03-neotokyo/contracts/s2/NTOuterCitizenDeploy.sol::977 => function _burn(uint256 tokenId) internal virtual {
2023-03-neotokyo/contracts/s2/NTOuterCitizenDeploy.sol::1456 => function identityValidated(uint256 identityId) internal view returns (bool) {
2023-03-neotokyo/contracts/s2/NTOuterCitizenDeploy.sol::1469 => function itemCacheValidated(uint256 itemCacheId) internal view returns (bool) {
2023-03-neotokyo/contracts/s2/NTOuterCitizenDeploy.sol::1474 => function landDeedValidated(uint256 landDeedId) internal view returns (bool) {
2023-03-neotokyo/contracts/s2/NTOuterIdentity.sol::257 => function _msgData() internal view virtual returns (bytes calldata) {
2023-03-neotokyo/contracts/s2/NTOuterIdentity.sol::486 => function sendValue(address payable recipient, uint256 amount) internal {
2023-03-neotokyo/contracts/s2/NTOuterIdentity.sol::762 => function _baseURI() internal view virtual returns (string memory) {
2023-03-neotokyo/contracts/s2/NTOuterIdentity.sol::940 => function _mint(address to, uint256 tokenId) internal virtual {
2023-03-neotokyo/contracts/s2/NTOuterIdentity.sol::962 => function _burn(uint256 tokenId) internal virtual {
2023-03-neotokyo/contracts/s2/NTOuterIdentity.sol::1487 => function validateCitizen() internal view returns(bool) {
2023-03-neotokyo/contracts/s2/NTOuterIdentity.sol::1532 => function whitelistValidated(address wallet, uint256 index, uint256 spotInLeaderboard, bytes32[] memory proof) internal view returns (bool) {
2023-03-neotokyo/contracts/s2/NTS2Items.sol::256 => function _msgData() internal view virtual returns (bytes calldata) {
2023-03-neotokyo/contracts/s2/NTS2Items.sol::485 => function sendValue(address payable recipient, uint256 amount) internal {
2023-03-neotokyo/contracts/s2/NTS2Items.sol::761 => function _baseURI() internal view virtual returns (string memory) {
2023-03-neotokyo/contracts/s2/NTS2Items.sol::939 => function _mint(address to, uint256 tokenId) internal virtual {
2023-03-neotokyo/contracts/s2/NTS2Items.sol::961 => function _burn(uint256 tokenId) internal virtual {
2023-03-neotokyo/contracts/s2/NTS2Items.sol::1374 => function identityValidated(uint256 identityId) internal view returns (bool) {
2023-03-neotokyo/contracts/s2/NTS2Items.sol::1380 => function whitelistValidated(uint256 identityTokenId, uint256 leaderboardSpot,uint256 index, bytes32[] memory proof) internal view returns (bool) {
2023-03-neotokyo/contracts/s2/NTS2LandDeploy.sol::256 => function _msgData() internal view virtual returns (bytes calldata) {
2023-03-neotokyo/contracts/s2/NTS2LandDeploy.sol::485 => function sendValue(address payable recipient, uint256 amount) internal {
2023-03-neotokyo/contracts/s2/NTS2LandDeploy.sol::761 => function _baseURI() internal view virtual returns (string memory) {
2023-03-neotokyo/contracts/s2/NTS2LandDeploy.sol::939 => function _mint(address to, uint256 tokenId) internal virtual {
2023-03-neotokyo/contracts/s2/NTS2LandDeploy.sol::961 => function _burn(uint256 tokenId) internal virtual {
2023-03-neotokyo/contracts/s2/NTS2LandDeploy.sol::1347 => function identityValidated(uint256 identityId) internal view returns (bool) {
2023-03-neotokyo/contracts/s2/NTS2LandDeploy.sol::1353 => function whitelistValidated(uint256 identityTokenId, uint256 leaderboardSpot,uint256 index, bytes32[] memory proof) internal view returns (bool) {
```

## [G-24] internal functions not called by the contract should be removed to save deployment gas

If the functions are required by an interface, the contract should inherit from that interface and use the override keyword

```
2023-03-neotokyo/contracts/s1/BYTESContract.sol::82 => function sendValue(address payable recipient, uint256 amount) internal {
2023-03-neotokyo/contracts/s1/BYTESContract.sol::189 => function _msgData() internal view virtual returns (bytes memory) {
2023-03-neotokyo/contracts/s1/BYTESContract.sol::775 => function _setupDecimals(uint8 decimals_) internal {
2023-03-neotokyo/contracts/s1/NTCitizenDeploy.sol::276 => function _msgData() internal view virtual returns (bytes calldata) {
2023-03-neotokyo/contracts/s1/NTCitizenDeploy.sol::505 => function sendValue(address payable recipient, uint256 amount) internal {
2023-03-neotokyo/contracts/s1/NTItems.sol::256 => function _msgData() internal view virtual returns (bytes calldata) {
2023-03-neotokyo/contracts/s1/NTItems.sol::485 => function sendValue(address payable recipient, uint256 amount) internal {
2023-03-neotokyo/contracts/s1/NTItems.sol::961 => function _burn(uint256 tokenId) internal virtual {
2023-03-neotokyo/contracts/s1/NTLandDeploy.sol::256 => function _msgData() internal view virtual returns (bytes calldata) {
2023-03-neotokyo/contracts/s1/NTLandDeploy.sol::485 => function sendValue(address payable recipient, uint256 amount) internal {
2023-03-neotokyo/contracts/s1/NTLandDeploy.sol::961 => function _burn(uint256 tokenId) internal virtual {
2023-03-neotokyo/contracts/s1/beckLoot.sol::253 => function _msgData() internal view virtual returns (bytes calldata) {
2023-03-neotokyo/contracts/s1/beckLoot.sol::482 => function sendValue(address payable recipient, uint256 amount) internal {
2023-03-neotokyo/contracts/s1/beckLoot.sol::958 => function _burn(uint256 tokenId) internal virtual {
2023-03-neotokyo/contracts/s1/vaultBox.sol::253 => function _msgData() internal view virtual returns (bytes calldata) {
2023-03-neotokyo/contracts/s1/vaultBox.sol::482 => function sendValue(address payable recipient, uint256 amount) internal {
2023-03-neotokyo/contracts/s1/vaultBox.sol::958 => function _burn(uint256 tokenId) internal virtual {
2023-03-neotokyo/contracts/s2/NTOuterCitizenDeploy.sol::272 => function _msgData() internal view virtual returns (bytes calldata) {
2023-03-neotokyo/contracts/s2/NTOuterCitizenDeploy.sol::501 => function sendValue(address payable recipient, uint256 amount) internal {
2023-03-neotokyo/contracts/s2/NTOuterIdentity.sol::257 => function _msgData() internal view virtual returns (bytes calldata) {
2023-03-neotokyo/contracts/s2/NTOuterIdentity.sol::486 => function sendValue(address payable recipient, uint256 amount) internal {
2023-03-neotokyo/contracts/s2/NTOuterIdentity.sol::962 => function _burn(uint256 tokenId) internal virtual {
2023-03-neotokyo/contracts/s2/NTS2Items.sol::256 => function _msgData() internal view virtual returns (bytes calldata) {
2023-03-neotokyo/contracts/s2/NTS2Items.sol::485 => function sendValue(address payable recipient, uint256 amount) internal {
2023-03-neotokyo/contracts/s2/NTS2Items.sol::961 => function _burn(uint256 tokenId) internal virtual {
2023-03-neotokyo/contracts/s2/NTS2LandDeploy.sol::256 => function _msgData() internal view virtual returns (bytes calldata) {
2023-03-neotokyo/contracts/s2/NTS2LandDeploy.sol::485 => function sendValue(address payable recipient, uint256 amount) internal {
2023-03-neotokyo/contracts/s2/NTS2LandDeploy.sol::961 => function _burn(uint256 tokenId) internal virtual {
```
