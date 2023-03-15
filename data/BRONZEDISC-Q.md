## QA
---

### Layout Order [1]

- The best-practices for layout within a contract is the following order: state variables, events, modifiers, constructor and functions.

https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/s2/NTS2Items.sol

```solidity
// modifier coming after constructor
296:    modifier onlyOwner() {

// modifier coming after constructor
373:    modifier nonReentrant() {
```

https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/s2/NTOuterCitizenDeploy.sol

```solidity
// modifier coming after construtor and function
312:    modifier onlyOwner() {

// modifier coming after constructor
389:    modifier nonReentrant() {


// internal function coming before public functions
777:    function _baseURI() internal view virtual returns (string memory) {

// private function coming before internal one
1042:    function _checkOnERC721Received(
```

https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/s1/beckLoot.sol

```solidity
// modifier coming after function
293:    modifier onlyOwner() {

// modifier coming after constructor
370:    modifier nonReentrant() {

// constructor coming after functions
1602:    constructor() ERC721("Neo Tokyo: Identities", "NEOTI") Ownable() {
```

https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/s1/NTItems.sol

```solidity
// modifier coming after constructor and function
296:    modifier onlyOwner() {

// modifier coming after constructor
373:    modifier nonReentrant() {

// constructor coming for last after functions
1632:    constructor() ERC721("Neo Tokyo Part 3 Item Caches", "NTITEM") Ownable() {
```

https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/s1/NTCitizenDeploy.sol

```solidity
// modifier coming after constructor
393:    modifier nonReentrant() {

// constructor coming after functions
1661:    constructor() ERC721("Neo Tokyo Citizen", "NTCTZN") Ownable() {
```

https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/s1/BYTESContract.sol

```solidity
// events coming after functions
262:    event Transfer(address indexed from, address indexed to, uint256 value);
268:    event Approval(address indexed owner, address indexed spender, uint256 value);

// modifier coming after function
463:    modifier onlyOwner() {
```

---

### Function Visibility [2]

- Order of Functions: Ordering helps readers identify which functions they can call and to find the constructor and fallback definitions easier. Functions should be grouped according to their visibility and ordered: constructor, receive function (if exists), fallback function (if exists), public, external, internal, private. Within a grouping, place the view and pure functions last.

https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/NeoTokyoStaker.sol

```solidity
// Public, External and Private functions are mixed throughout the code, order them.
```

https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/s2/NTOuterIdentity.sol

```solidity
// public functions coming after externals and internals
1320:    function getMostBytesBurned() public view returns(uint256)
1338:    function getClass(uint256 tokenId) public view returns (string memory) {
1348:    function getRace(uint256 tokenId) public view returns (string memory) {
1358:    function getNose(uint256 tokenId) public view returns (string memory) {
1368:    function getHair(uint256 tokenId) public view returns (string memory) {
1378:    function getExpression(uint256 tokenId) public view returns (string memory) {
1388:    function getStrength(uint256 tokenId) public view returns (string memory) {
1398:    function getAttractiveness(uint256 tokenId) public view returns (string memory) {
1408:    function getTechSkill(uint256 tokenId) public view returns (string memory) {
1418:    function getCool(uint256 tokenId) public view returns (string memory) {
1428:    function getEyes(uint256 tokenId) public view returns (string memory) {
1438:    function getAbility(uint256 tokenId) public view returns (string memory) {
1448:    function tokenURI(uint256 tokenId) override public view returns (string memory) {
1476:    function reserveIdentity() public nonReentrant payable {
1497:    function burnBytes(uint256 amount) public nonReentrant
1514:    function whitelistClaim(uint256 tokenId, uint256 spotInWhitelist, bytes32[] memory proof) public nonReentrant {
1526:    function ownerClaim(uint256 tokenId) public nonReentrant onlyOwner {
1558:    function setOuterIdentityMintContract(address contractAddress) public onlyOwner {
```

https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/s1/beckLoot.sol

```solidity
// internal function coming before public function
758:    function _baseURI() internal view virtual returns (string memory) {

// external functions coming before public functions
1356:    function setSale() external onlyOwner
1361:    function setWhitelistState() external onlyOwner
1366:    function getCredits(uint256 tokenId) external view returns (string memory)

// internal function coming before public ones
1521:    function whitelistValidated(address wallet, uint256 index, bytes32[] memory proof) internal view returns (bool) {
```

https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/s1/NTItems.sol

```solidity
// internal fn coming before public ones
761:    function _baseURI() internal view virtual returns (string memory) {

// private fn coming before internal one
1026:    function _checkOnERC721Received(

// external funtions coming before public ones
1432:    function setItemMint() external onlyOwner
1437:    function checkSpecialItems(uint256 tokenId) external view returns (string memory)
1453:    function getVehicle(uint256 tokenId) external view returns (string memory)

// internal fn before public ones
1524:    function specialItemCheck(uint256 spotOnLeaderboard) internal
1557:    function boxValidated(uint256 boxId) internal view returns (bool) {
1563:    function whitelistValidated(string memory vaultTokenId, uint256 leaderboardSpot,uint256 index, bytes32[] memory proof) internal view returns (bool) {
```

https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/s1/NTCitizenDeploy.sol

```solidity
// external view functions coming before non-view external functions
79:    function balanceOf(address owner) external view returns (uint256 balance);
88:    function ownerOf(uint256 tokenId) external view returns (address owner);
152:    function getApproved(uint256 tokenId) external view returns (address operator);
171:    function isApprovedForAll(address owner, address operator) external view returns (bool);

// internal function coming before public ones
781:    function _baseURI() internal view virtual returns (string memory) {

// internal function coming before private function.
1083:    function _beforeTokenTransfer(

// internal function coming before public one
1375:    function rewardRateByTokenId(uint256 tokenId) internal view returns(uint256){

// public function coming after internal one
1410:    function createCitizen(uint256 identityId, uint256 vaultId, uint256 itemCacheId, uint256 landDeedId, bool genderFemale, string memory specialMessage) public nonReentrant {

// internal functions coming before public and external functions
1539:    function vaultValidated(uint256 vaultId) internal view returns (bool) {
1544:    function identityValidated(uint256 identityId) internal view returns (bool) {
1557:    function itemCacheValidated(uint256 itemCacheId) internal view returns (bool) {
1562:    function landDeedValidated(uint256 landDeedId) internal view returns (bool) {
```

https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/s1/BYTESContract.sol

```solidity
// internal function coming before public ones
879:    function validateIdentity(uint256 tokenId) internal view returns(bool)

// public function coming for last
999:    function strToUint(string memory _str) public pure returns(uint256 res, bool err) {
```

https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/access/PermitControl.sol

```solidity
// external function coming before public ones
124:	function setManagerRight (
```

---

### natSpec missing [3]

https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/s2/NTS2LandDeploy.sol

```solidity
12:   interface IByteContract {

13:    function burn(address _from, uint256 _amount) external;

252:    function _msgSender() internal view virtual returns (address) {

256:    function _msgData() internal view virtual returns (bytes calldata) {

321:    function _setOwner(address newOwner) private {

362:    constructor() {

620:    function _verifyCallResult(

1252:   contract NTS2LandDeploy is ERC721Enumerable, ReentrancyGuard, Ownable {

1284:    function getLocation(uint256 tokenId) public view returns (string memory)

1295:    function getTokenClaimedByIdentityTokenId(uint256 identityTokenId) public view returns (uint256) {

1299:    function getClaimantIdentityIdByTokenId(uint256 tokenId) public view returns (uint256) {

1303:    function tokenURI(uint256 tokenId) override public view returns (string memory) {

1314:    function landClaim(uint256 identityTokenId, uint256 spotOnLeaderboard, uint256 spotInWhitelist, bytes32[] memory proof) public nonReentrant {

1328:    function buyLand() public nonReentrant

1338:    function emergencyClaim(uint256 identityId, uint256 tokenId) public onlyOwner {

1347:    function identityValidated(uint256 identityId) internal view returns (bool) {

1353:    function whitelistValidated(uint256 identityTokenId, uint256 leaderboardSpot,uint256 index, bytes32[] memory proof) internal view returns (bool) {

1379:    function setLandMintActive() public onlyOwner {

1383:    function setLandCost(uint256 _cost) public onlyOwner {

1387:    function setContract(address contractAddress) public onlyOwner {

1391:    function setIdentityContract(address contractAddress) public onlyOwner {

1395:    function setBytesAddress(address contractAddress) public onlyOwner {

1400:    function toString(uint256 value) internal pure returns (string memory) {

1422:    constructor() ERC721("Neo Tokyo Part 4 Land Deeds", "NTLD") Ownable() {
```

https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/s2/NTS2Items.sol

```solidity
12:   interface IByteContract {

13:    function burn(address _from, uint256 _amount) external;

252:    function _msgSender() internal view virtual returns (address) {

256:    function _msgData() internal view virtual returns (bytes calldata) {

321:    function _setOwner(address newOwner) private {

362:    constructor() {

620:    function _verifyCallResult(

1252:   contract NTS2Items is ERC721Enumerable, ReentrancyGuard, Ownable {

1269:    function setItemMint() external onlyOwner

1274:    function getWeapon(uint256 tokenId) public view returns (string memory)

1285:    function getVehicle(uint256 tokenId) external view returns (string memory)

1296:    function getApparel(uint256 tokenId) public view returns (string memory) {

1306:    function getHelm(uint256 tokenId) public view returns (string memory) {

1316:    function getTokenClaimedByIdentityTokenId(uint256 identityTokenId) public view returns (uint256) {

1320:    function getClaimantIdentityIdByTokenId(uint256 tokenId) public view returns (uint256) {

1324:    function addRootHash(bytes32 _hash) external onlyOwner {

1328:    function tokenURI(uint256 tokenId) override public view returns (string memory) {

1340:    function identityClaim(uint256 identityTokenId, uint256 spotOnLeaderboard, uint256 spotInWhitelist, bytes32[] memory proof) public nonReentrant {

1355:    function buyItems() public nonReentrant

1365:    function emergencyClaim(uint256 identityId, uint256 tokenId) public onlyOwner {

1374:    function identityValidated(uint256 identityId) internal view returns (bool) {

1380:    function whitelistValidated(uint256 identityTokenId, uint256 leaderboardSpot,uint256 index, bytes32[] memory proof) internal view returns (bool) {

1406:    function setItemMintActive() public onlyOwner {

1410:    function setItemCost(uint256 _cost) public onlyOwner {

1414:    function setContract(address contractAddress) public onlyOwner {

1418:    function setIdentityAddress(address contractAddress) public onlyOwner {

1422:    function setBytesAddress(address contractAddress) public onlyOwner {

1427:    function toString(uint256 value) internal pure returns (string memory) {

1449:    constructor() ERC721("Neo Tokyo Outer Item Caches", "NTOITEM") Ownable() {
```

https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/s2/NTOuterIdentity.sol

```solidity
12:   interface IByteContract {

13:    function burn(address _from, uint256 _amount) external;

322:    function _setOwner(address newOwner) private {

353:    constructor() {

1254:   contract NTOuterIdentity is ERC721Enumerable, ReentrancyGuard, Ownable {

1283:    function setSaleActive() external onlyOwner

1290:    function setWhitelistState() external onlyOwner

1295:    function setBurnState() external onlyOwner

1300:    function addRootHash(bytes32 _hash) external onlyOwner

1305:    function setMaxMints(uint256 _value) external onlyOwner

1310:    function setCitizenContract(address _address) external onlyOwner

1315:    function setIdentityContract(address _address) external onlyOwner

1320:    function getMostBytesBurned() public view returns(uint256)

1325:    function startBurn() external onlyOwner

1332:    function changeTimeToBurn(uint256 _time) external onlyOwner

1338:    function getClass(uint256 tokenId) public view returns (string memory) {

1348:    function getRace(uint256 tokenId) public view returns (string memory) {

1358:    function getNose(uint256 tokenId) public view returns (string memory) {

1368:    function getHair(uint256 tokenId) public view returns (string memory) {

1378:    function getExpression(uint256 tokenId) public view returns (string memory) {

1388:    function getStrength(uint256 tokenId) public view returns (string memory) {

1398:    function getAttractiveness(uint256 tokenId) public view returns (string memory) {

1408:    function getTechSkill(uint256 tokenId) public view returns (string memory) {

1418:    function getCool(uint256 tokenId) public view returns (string memory) {

1428:    function getEyes(uint256 tokenId) public view returns (string memory) {

1438:    function getAbility(uint256 tokenId) public view returns (string memory) {

1448:    function tokenURI(uint256 tokenId) override public view returns (string memory) {

1459:    function timeUntilBytesAuctionEnds() external view returns(uint256)

1466:    function timeUntilPublicMint() external view returns(uint256)

1473:    function reserveIdentity() public nonReentrant payable {

1487:    function validateCitizen() internal view returns(bool) {

1497:    function burnBytes(uint256 amount) public nonReentrant

1514:    function whitelistClaim(uint256 tokenId, uint256 spotInWhitelist, bytes32[] memory proof) public nonReentrant {

1526:    function ownerClaim(uint256 tokenId) public nonReentrant onlyOwner {

1532:    function whitelistValidated(address wallet, uint256 index, uint256 spotInLeaderboard, bytes32[] memory proof) internal view returns (bool) {

1558:    function setOuterIdentityMintContract(address contractAddress) public onlyOwner {

1562:    function setWithdrawalAddress(address _address) external onlyOwner

1567:    function withdraw() external onlyOwner

1573:    function toString(uint256 value) internal pure returns (string memory) {

1595:    constructor() ERC721("Neo Tokyo: Outer Identities", "NEOTOI") Ownable() {
```

https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/s2/NTOuterCitizenDeploy.sol

```solidity
12: interface IByteContract {

13:    function burn(address _from, uint256 _amount) external;

14:    function getReward(address _to) external;

15:    function updateRewardOnMint(address _user, uint256 tokenId) external;

16:    function updateReward(address _from, address _to, uint256 _tokenId) external;

19:   interface IMintContract {

20:    function generateURI(uint256 citizenId) external view returns (string memory);

21:    function calculateRewardRate(uint256 identityId, uint256 vaultId) external view returns(uint256);

24:   interface IIdentityContract {  

25:    function getClass(uint256 tokenId) external view returns (string memory);

28:   interface IBoughtIdentityContract {

29:    function claimIdentity(address _to) external;

378:    constructor() {

636:    function _verifyCallResult(

1270:   contract BytProxyRegistry {

1274:   contract NTOuterCitizenDeploy is ERC721Enumerable, IERC721Receiver, ReentrancyGuard, Ownable {

1307:    function getIdentityIdOfTokenId(uint256 citizenId) public view returns (uint256) {

1310:    function getItemCacheIdOfTokenId(uint256 citizenId) public view returns (uint256) {

1313:    function getLandDeedIdOfTokenId(uint256 citizenId) public view returns (uint256) {

1316:    function getSpecialMessageOfTokenId(uint256 citizenId) public view returns (string memory) {

1319:    function getGenderOfTokenId(uint256 citizenId) public view returns (bool) {

1223:    function onERC721Received(

1332:    function tokenURI(uint256 tokenId) override public view returns (string memory) {

1352:    function createCitizen(uint256 identityId, uint256 itemCacheId, uint256 landDeedId, bool genderFemale, string memory specialMessage) public nonReentrant {

1401:    function disassembleCitizen(uint256 citizenId)public nonReentrant {

1431:     function changeGender(uint256 tokenId) public nonReentrant {

1440:     function changeSpecialMessage(uint256 tokenId, string memory _message) public nonReentrant {

1448:     function setChangeGenderCost(uint256 _cost) external onlyOwner {

1452:     function setChangeMessageCost(uint256 _cost) external onlyOwner {

1456:    function identityValidated(uint256 identityId) internal view returns (bool) {

1469:    function itemCacheValidated(uint256 itemCacheId) internal view returns (bool) {

1474:    function landDeedValidated(uint256 landDeedId) internal view returns (bool) {

1479:    function setFemaleActive() public onlyOwner {

1483:    function setCitizenMintActive() public onlyOwner {

1487:    function setBoughtIdentitiesActive() public onlyOwner {

1491:    function setBoughtIdentityContract(address _address) external onlyOwner {

1495:    function setIdentityAddress(address contractAddress) public onlyOwner {

1499:    function setLandContract(address contractAddress) public onlyOwner {

1503:    function setItemContract(address contractAddress) public onlyOwner {

1507:    function setBytesAddress(address contractAddress) public onlyOwner {

1511:    function setCitizenMintContract(address contractAddress) public onlyOwner {

1515:    function setCitizenAlternateMintContract(address contractAddress) public onlyOwner {

1519:    function setMintedIdentityCost(uint256 _cost) public onlyOwner {

1523:    function mintIdentity() public

1533:    function isApprovedForAll(address _owner, address operator) public view override returns (bool) {

1539:    function toString(uint256 value) internal pure returns (string memory) {

1561:    constructor() ERC721("Neo Tokyo Outer Citizen", "NTOCTZN") Ownable() {
```

https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/s1/vaultBox.sol

```solidity
318:    function _setOwner(address newOwner) private {

359:    constructor() {

617:    function _verifyCallResult(

1250:   contract vaultBox is ERC721Enumerable, ReentrancyGuard, Ownable {

1338:    function setSale() external onlyOwner

1343:    function setWhitelistState() external onlyOwner

1348:    function setOpenClaimState() external onlyOwner

1353:    function getCreditProportionOfTotalSupply(uint256 tokenId) public view returns (string memory)

1364:    function getCredits(uint256 tokenId) external view returns (string memory)

1375:    function getAdditionalItem(uint256 tokenId) public view returns (string memory) {

1385:    function getCreditMultiplier(uint256 tokenId) public view returns (string memory) {

1395:    function getTokenClaimedByIdentityTokenId(uint256 identityTokenId) public view returns (uint256) {

1399:    function getClaimantIdentityIdByTokenId(uint256 tokenId) public view returns (uint256) {

1403:    function tokenURI(uint256 tokenId) override public view returns (string memory) {

1424:    function holderClaim(uint256 tokenId) public nonReentrant {

1440:    function whitelistClaim(uint256 tokenId, uint256 spotInWhitelist, bytes32[] memory proof) public nonReentrant {

1452:    function claim(uint256 tokenId) public nonReentrant {

1459:    function ownerClaim(uint256 tokenId) public nonReentrant onlyOwner {

1464:    function holderValidated(address requester) internal view returns (uint256) {

1485:    function whitelistValidated(address wallet, uint256 index, bytes32[] memory proof) internal view returns (bool) {

1512:    function setContract(address contractAddress) public onlyOwner {

1516:    function setIdAddress(address contractAddress) public onlyOwner {

1521:    function toString(uint256 value) internal pure returns (string memory) {

1543:    constructor() ERC721("Neo Tokyo Part 2: Vault Cards", "NEOTV") Ownable() {
```

https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/s1/beckLoot.sol

```solidity
249:    function _msgSender() internal view virtual returns (address) {

253:    function _msgData() internal view virtual returns (bytes calldata) {

318:    function _setOwner(address newOwner) private {

359:    constructor() {

1250:  contract beckLoot is ERC721Enumerable, ReentrancyGuard, Ownable {

1356:    function setSale() external onlyOwner

1361:    function setWhitelistState() external onlyOwner

1366:    function getCredits(uint256 tokenId) external view returns (string memory)

1377:    function getClass(uint256 tokenId) public view returns (string memory) {

1387:    function getRace(uint256 tokenId) public view returns (string memory) {

1397:    function getStrength(uint256 tokenId) public view returns (string memory) {

1407:    function getIntelligence(uint256 tokenId) public view returns (string memory) {

1417:    function getAttractiveness(uint256 tokenId) public view returns (string memory) {

1427:    function getTechSkill(uint256 tokenId) public view returns (string memory) {

1437:    function getCool(uint256 tokenId) public view returns (string memory) {

1447:    function getEyes(uint256 tokenId) public view returns (string memory) {

1457:    function getGender(uint256 tokenId) public view returns (string memory) {

1467:    function getAbility(uint256 tokenId) public view returns (string memory) {

1477:    function tokenURI(uint256 tokenId) override public view returns (string memory) {

1488:    function claim(uint256 tokenId) public nonReentrant {

1494:    function whitelistClaim(uint256 tokenId, uint256 spotInWhitelist, bytes32[] memory proof) public nonReentrant {

1506:    function ownerClaim(uint256 tokenId) public nonReentrant onlyOwner {

1511:    function riddleClaim(address _to, uint256 tokenId) public nonReentrant onlyOwner {

1516:    function handClaim(address _to, uint256 tokenId) public nonReentrant onlyOwner {

1521:    function whitelistValidated(address wallet, uint256 index, bytes32[] memory proof) internal view returns (bool) {

1548:    function setMintContract(address contractAddress) public onlyOwner {

1552:    function setRareMintContract(address contractAddress) public onlyOwner {

1556:    function setHandMintContract(address contractAddress) public onlyOwner {

1560:    function contractRouter(uint256 tokenId) internal view returns (address) {

1580:    function toString(uint256 value) internal pure returns (string memory) {

1602:    constructor() ERC721("Neo Tokyo: Identities", "NEOTI") Ownable() {
```


https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/s1/NTLandDeploy.sol

```solidity
12:   interface IByteContract {

13:    function burn(address _from, uint256 _amount) external;

252:    function _msgSender() internal view virtual returns (address) {

256:    function _msgData() internal view virtual returns (bytes calldata) {

321:    function _setOwner(address newOwner) private {

620:    function _verifyCallResult(

1252:   contract NTLandDeploy is ERC721Enumerable, ReentrancyGuard, Ownable {

1296:    function getLocation(uint256 tokenId) public view returns (string memory)

1307:    function getTokenClaimedByVaultTokenId(uint256 vaultTokenId) public view returns (uint256) {

1310:    function getTokenClaimedByIdentityTokenId(uint256 identityTokenId) public view returns (uint256) {

1313:    function getTokenClaimedByItemCacheTokenId(uint256 itemCacheTokenId) public view returns (uint256) {

1317:    function getClaimantVaultIdByTokenId(uint256 tokenId) public view returns (uint256) {

1320:    function getClaimantIdentityIdByTokenId(uint256 tokenId) public view returns (uint256) {

1323:    function getClaimantItemCacheIdByTokenId(uint256 tokenId) public view returns (uint256) {

1327:    function tokenURI(uint256 tokenId) override public view returns (string memory) {

1338:    function landClaim(uint256 vaultTokenId, uint256 identityTokenId, uint256 itemCacheTokenId, uint256 spotOnLeaderboard, uint256 spotInWhitelist, bytes32[] memory proof) public nonReentrant {

1363:    function buyLand() public nonReentrant

1373:    function emergencyClaim(uint256 vaultId, uint256 tokenId) public onlyOwner {

1382:    function vaultValidated(uint256 vaultId) internal view returns (bool) {

1388:    function identityValidated(uint256 identityId) internal view returns (bool) {

1394:    function itemCacheValidated(uint256 itemCacheId) internal view returns (bool) {

1400:    function whitelistValidated(string memory vaultTokenId, uint256 leaderboardSpot,uint256 index, bytes32[] memory proof) internal view returns (bool) {

1426:    function setLandMintActive() public onlyOwner {

1430:    function setLandCost(uint256 _cost) public onlyOwner {

1434:    function setContract(address contractAddress) public onlyOwner {

1438:    function setItemContract(address contractAddress) public onlyOwner {

1442:    function setVaultAddress(address contractAddress) public onlyOwner {

1446:    function setBytesAddress(address contractAddress) public onlyOwner {

1451:    function toString(uint256 value) internal pure returns (string memory) {

1473:    constructor() ERC721("Neo Tokyo Part 4 Land Deeds", "NTLD") Ownable() {
```

https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/s1/NTItems.sol

```solidity
252:    function _msgSender() internal view virtual returns (address) {

256:    function _msgData() internal view virtual returns (bytes calldata) {

620:    function _verifyCallResult(

1252:   contract NTItems is ERC721Enumerable, ReentrancyGuard, Ownable {

1432:    function setItemMint() external onlyOwner

1437:    function checkSpecialItems(uint256 tokenId) external view returns (string memory)

1442:    function getWeapon(uint256 tokenId) public view returns (string memory)

1453:    function getVehicle(uint256 tokenId) external view returns (string memory)

1464:    function getApparel(uint256 tokenId) public view returns (string memory) {

1474:    function getHelm(uint256 tokenId) public view returns (string memory) {

1484:    function getTokenClaimedByBoxTokenId(uint256 boxTokenId) public view returns (uint256) {

1488:    function getClaimantBoxIdByTokenId(uint256 tokenId) public view returns (uint256) {

1492:    function tokenURI(uint256 tokenId) override public view returns (string memory) {

1504:    function boxClaim(uint256 boxTokenId, uint256 spotOnLeaderboard, uint256 spotInWhitelist, bytes32[] memory proof) public nonReentrant {

1524:    function specialItemCheck(uint256 spotOnLeaderboard) internal

1538:    function buyItems() public nonReentrant

1548:    function emergencyClaim(uint256 boxId, uint256 tokenId) public onlyOwner {

1557:    function boxValidated(uint256 boxId) internal view returns (bool) {

1563:    function whitelistValidated(string memory vaultTokenId, uint256 leaderboardSpot,uint256 index, bytes32[] memory proof) internal view returns (bool) {

1589:    function setItemMintActive() public onlyOwner {

1593:    function setItemCost(uint256 _cost) public onlyOwner {

1597:    function setContract(address contractAddress) public onlyOwner {

1601:    function setBoxAddress(address contractAddress) public onlyOwner {

1605:    function setBytesAddress(address contractAddress) public onlyOwner {

1610:    function toString(uint256 value) internal pure returns (string memory) {

1632:    constructor() ERC721("Neo Tokyo Part 3 Item Caches", "NTITEM") Ownable() {
```

https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/s1/NTCitizenDeploy.sol

```solidity
12: interface IByteContract {

13:    function burn(address _from, uint256 _amount) external;

14:    function getReward(address _to) external;

15:    function updateRewardOnMint(address _user, uint256 tokenId) external;

16:    function updateReward(address _from, address _to, uint256 _tokenId) external;

19:   interface IMintContract {

20:    function generateURI(uint256 citizenId) external view returns (string memory);

21:    function calculateRewardRate(uint256 identityId, uint256 vaultId) external view returns(uint256);

24:   interface IIdentityContract {  

25:    function getClass(uint256 tokenId) external view returns (string memory);

28:   interface IVaultContract {

29:    function getCreditMultiplier(uint256 tokenId) external view returns (string memory);

32:   interface IBoughtIdentityContract {

33:    function claimIdentity(address _to) external;

272:    function _msgSender() internal view virtual returns (address) {

276:    function _msgData() internal view virtual returns (bytes calldata) {

341:    function _setOwner(address newOwner) private {

382:    constructor() {

640:    function _verifyCallResult(

1272:   contract NTCitizenDeploy is ERC721Enumerable, IERC721Receiver, ReentrancyGuard, Ownable {

1310:    function getIdentityIdOfTokenId(uint256 citizenId) public view returns (uint256) {

1313:    function getVaultIdOfTokenId(uint256 citizenId) public view returns (uint256) {

1316:    function getItemCacheIdOfTokenId(uint256 citizenId) public view returns (uint256) {

1319:    function getLandDeedIdOfTokenId(uint256 citizenId) public view returns (uint256) {

1322:    function getSpecialMessageOfTokenId(uint256 citizenId) public view returns (string memory) {

1325:    function getRewardRateOfTokenId(uint256 citizenId) public view returns (uint256) {

1328:    function getGenderOfTokenId(uint256 citizenId) public view returns (bool) {

1332:    function onERC721Received(

1341:    function tokenURI(uint256 tokenId) override public view returns (string memory) {

1361:    function getRewardRate(address _user) public view returns(uint256) {

1370:    function getRewardsRateForTokenId(uint256 tokenId) public view returns(uint256){

1375:    function rewardRateByTokenId(uint256 tokenId) internal view returns(uint256){

1384:    function getCurrentOrFinalTime() public view returns(uint256) {

1401:    function getEnd() public view returns(uint256) {

1405:    function calculateCitizenReward(uint256 identityId, uint256 vaultId) internal view returns(uint256){

1410:    function createCitizen(uint256 identityId, uint256 vaultId, uint256 itemCacheId, uint256 landDeedId, bool genderFemale, string memory specialMessage) public nonReentrant {

1476:    function disassembleCitizen(uint256 citizenId)public nonReentrant {

1514:     function changeGender(uint256 tokenId) public nonReentrant {

1523:     function changeSpecialMessage(uint256 tokenId, string memory _message) public nonReentrant {

1531:     function setChangeGenderCost(uint256 _cost) external onlyOwner {

1535:     function setChangeMessageCost(uint256 _cost) external onlyOwner {

1539:    function vaultValidated(uint256 vaultId) internal view returns (bool) {

1544:    function identityValidated(uint256 identityId) internal view returns (bool) {

1557:    function itemCacheValidated(uint256 itemCacheId) internal view returns (bool) {

1562:    function landDeedValidated(uint256 landDeedId) internal view returns (bool) {

1567:    function setFemaleActive() public onlyOwner {

1571:    function setCitizenMintActive() public onlyOwner {

1575:    function setBoughtIdentitiesActive() public onlyOwner {

1579:    function setIdentityAddress(address contractAddress) public onlyOwner {

1583:    function setLandContract(address contractAddress) public onlyOwner {

1587:    function setItemContract(address contractAddress) public onlyOwner {

1591:    function setVaultAddress(address contractAddress) public onlyOwner {

1595:    function setBytesAddress(address contractAddress) public onlyOwner {

1599:    function setCitizenMintContract(address contractAddress) public onlyOwner {

1603:    function setCitizenAlternateMintContract(address contractAddress) public onlyOwner {

1607:    function setMintedIdentityCost(uint256 _cost) public onlyOwner {

1611:    function mintIdentity() public

1621:    function getReward() external {

1627:	function transferFrom(address from, address to, uint256 tokenId) public override {

1633:	function safeTransferFrom(address from, address to, uint256 tokenId, bytes memory _data) public override {

1639:    function toString(uint256 value) internal pure returns (string memory) {

1661:    constructor() ERC721("Neo Tokyo Citizen", "NTCTZN") Ownable() {
```

https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/s1/BYTESContract.sol

```solidity
9:  interface ICitizen {

10:	function getRewardRate(address _user) external view returns(uint256);

12:    function getRewardsRateForTokenId(uint256) external view returns(uint256);

14:    function getCurrentOrFinalTime() external view returns(uint256);

16:    function reduceRewards(uint256, address) external;

18:    function increaseRewards(uint256, address) external;

20:    function getEnd() external returns(uint256);

23:   interface IIdentity {

24:    function ownerOf(uint256 tokenId) external view returns (address owner);

27: interface IVaultBox {

28:	function getCredits(uint256 tokenId) external view returns (string memory);

29:    function ownerOf(uint256 tokenId) external view returns (address owner);

798:   contract BYTESContract is Ownable, ERC20("BYTES", "BYTES") {

817:	constructor() {

823:    function openVaultBox(uint256 identityTokenId, uint256 vaultBoxTokenId) public 

844:    function hasVaultBoxBeenOpened(uint256 tokenId) public view returns(bool)

854:    function hasIdentityOpenedABox(uint256 tokenId) public view returns(bool)

864:    function updateMaxRewardableTokens(uint256 _amount) public onlyOwner

869:    function addAdminContractAddress(address _address) public onlyOwner

874:    function removeAdminContactAddress(address _address) public onlyOwner

879:    function validateIdentity(uint256 tokenId) internal view returns(bool)

900:    function setIdentityContract(address _address) public onlyOwner {

904:    function setBoughtIdentityContract(address _address) public onlyOwner {

908:    function setVaultBoxContract(address _address) public onlyOwner {

912:    function setCitizenContract(address _address) public onlyOwner {

939:	function updateReward(address _from, address _to, uint256 _tokenId) external {

976:	function getReward(address _to) external {

986:	function burn(address _from, uint256 _amount) external {

991:	function getTotalClaimable(address _user) external view returns(uint256) {

999:    function strToUint(string memory _str) public pure returns(uint256 res, bool err) {
```

---

### State variable and function names [4]

- Variables should be named according to their specifications
- private and internal `variables` should preppend with `underline`
- private and internal `functions` should preppend with `underline`
- constant state variables should be UPPER_CASE

https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/s2/NTS2LandDeploy.sol

```solidity
private and internal `functions` should preppend with `underline`
185:    function toString(uint256 value) internal pure returns (string memory) {
211:    function toHexString(uint256 value) internal pure returns (string memory) {
227:    function toHexString(uint256 value, uint256 length) internal pure returns (string memory) {
457:    function isContract(address account) internal view returns (bool) {
485:    function sendValue(address payable recipient, uint256 amount) internal {
510:    function functionCall(address target, bytes memory data) internal returns (bytes memory) {
520:    function functionCall(
539:    function functionCallWithValue(
553:    function functionCallWithValue(
582:    function functionStaticCall(
609:    function functionDelegateCall(
1347:    function identityValidated(uint256 identityId) internal view returns (bool) {
1353:    function whitelistValidated(uint256 identityTokenId, uint256 leaderboardSpot,uint256 index, bytes32[] memory proof) internal view returns (bool) {
1400:    function toString(uint256 value) internal pure returns (string memory) {

//  private and internal `variables` should preppend with `underline`
1270:    string[] private location = [
```

https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/s2/NTS2Items.sol

```solidity
//  private and internal `functions` should preppend with `underline`
185:    function toString(uint256 value) internal pure returns (string memory) {
211:    function toHexString(uint256 value) internal pure returns (string memory) {
227:    function toHexString(uint256 value, uint256 length) internal pure returns (string memory) {
457:    function isContract(address account) internal view returns (bool) {
485:    function sendValue(address payable recipient, uint256 amount) internal {
510:    function functionCall(address target, bytes memory data) internal returns (bytes memory) {
520:    function functionCall(
539:    function functionCallWithValue(
553:    function functionCallWithValue(
572:    function functionStaticCall(address target, bytes memory data) internal view returns (bytes memory) {
582:    function functionStaticCall(
599:    function functionDelegateCall(address target, bytes memory data) internal returns (bytes memory) {
609:    function functionDelegateCall(
1374:    function identityValidated(uint256 identityId) internal view returns (bool) {
1380:    function whitelistValidated(uint256 identityTokenId, uint256 leaderboardSpot,uint256 index, bytes32[] memory proof) internal view returns (bool) {
1427:    function toString(uint256 value) internal pure returns (string memory) {
```

https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/s2/NTOuterIdentity.sol

```solidity
//  private and internal `functions` should preppend with `underline`
186:    function toString(uint256 value) internal pure returns (string memory) {
212:    function toHexString(uint256 value) internal pure returns (string memory) {
228:    function toHexString(uint256 value, uint256 length) internal pure returns (string memory) {
458:    function isContract(address account) internal view returns (bool) {
486:    function sendValue(address payable recipient, uint256 amount) internal {
511:    function functionCall(address target, bytes memory data) internal returns (bytes memory) {
521:    function functionCall(
540:    function functionCallWithValue(
554:    function functionCallWithValue(
573:    function functionStaticCall(address target, bytes memory data) internal view returns (bytes memory) {
583:    function functionStaticCall(
600:    function functionDelegateCall(address target, bytes memory data) internal returns (bytes memory) {
610:    function functionDelegateCall(
1487:    function validateCitizen() internal view returns(bool) {
1532:    function whitelistValidated(address wallet, uint256 index, uint256 spotInLeaderboard, bytes32[] memory proof) internal view returns (bool) {
1573:    function toString(uint256 value) internal pure returns (string memory) {
```

https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/s2/NTOuterCitizenDeploy.sol

```solidity
// private and internal `functions` should preppend with `underline`
201:    function toString(uint256 value) internal pure returns (string memory) {
227:    function toHexString(uint256 value) internal pure returns (string memory) {
243:    function toHexString(uint256 value, uint256 length) internal pure returns (string memory) {
473:    function isContract(address account) internal view returns (bool) {
501:    function sendValue(address payable recipient, uint256 amount) internal {
536:    function functionCall(
555:    function functionCallWithValue(
569:    function functionCallWithValue(
588:    function functionStaticCall(address target, bytes memory data) internal view returns (bytes memory) {
598:    function functionStaticCall(
615:    function functionDelegateCall(address target, bytes memory data) internal returns (bytes memory) {
625:    function functionDelegateCall(
1456:    function identityValidated(uint256 identityId) internal view returns (bool) {
1469:    function itemCacheValidated(uint256 itemCacheId) internal view returns (bool) {
1474:    function landDeedValidated(uint256 landDeedId) internal view returns (bool) {
1539:    function toString(uint256 value) internal pure returns (string memory) {
```

https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/s1/vaultBox.sol

```solidity
// private and internal `functions` should preppend with `underline`
182:    function toString(uint256 value) internal pure returns (string memory) {
208:    function toHexString(uint256 value) internal pure returns (string memory) {
224:    function toHexString(uint256 value, uint256 length) internal pure returns (string memory) {
454:    function isContract(address account) internal view returns (bool) {
482:    function sendValue(address payable recipient, uint256 amount) internal {
507:    function functionCall(address target, bytes memory data) internal returns (bytes memory) {
517:    function functionCall(
536:    function functionCallWithValue(
550:    function functionCallWithValue(
569:    function functionStaticCall(address target, bytes memory data) internal view returns (bytes memory) {
579:    function functionStaticCall(
596:    function functionDelegateCall(address target, bytes memory data) internal returns (bytes memory) {
606:    function functionDelegateCall(
1464:    function holderValidated(address requester) internal view returns (uint256) {
1485:    function whitelistValidated(address wallet, uint256 index, bytes32[] memory proof) internal view returns (bool) {
1521:    function toString(uint256 value) internal pure returns (string memory) {

// private and internal `variables` should preppend with `underline`
1268:    string[] private class = [
1305:    string[] private additionalItem = [
1329:    string[] private creditMultiplier = [
```

https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/s1/beckLoot.sol

```solidity
// private and internal `functions` should preppend with `underline`
182:    function toString(uint256 value) internal pure returns (string memory) {
208:    function toHexString(uint256 value) internal pure returns (string memory) {
224:    function toHexString(uint256 value, uint256 length) internal pure returns (string memory) {
454:    function isContract(address account) internal view returns (bool) {
482:    function sendValue(address payable recipient, uint256 amount) internal {
507:    function functionCall(address target, bytes memory data) internal returns (bytes memory) {
517:    function functionCall(
536:    function functionCallWithValue(
550:    function functionCallWithValue(
569:    function functionStaticCall(address target, bytes memory data) internal view returns (bytes memory) {
579:    function functionStaticCall(
596:    function functionDelegateCall(address target, bytes memory data) internal returns (bytes memory) {
606:    function functionDelegateCall(
1250:   contract beckLoot is ERC721Enumerable, ReentrancyGuard, Ownable {
1521:    function whitelistValidated(address wallet, uint256 index, bytes32[] memory proof) internal view returns (bool) {
1560:    function contractRouter(uint256 tokenId) internal view returns (address) {
1580:    function toString(uint256 value) internal pure returns (string memory) {

// private and internal `functions` should preppend with `underline`
1263:    string[] private class = [
1300:    string[] private gender = [
1305:    string[] private race = [
1317:    string[] private ability = [
1329:    string[] private eyes = [
1348:    string[] private creditYield = [
```

https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/s1/NTLandDeploy.sol

```solidity
// private and internal `variables` should preppend with `underline`
1281:    string[] private location = [

// private and internal `functions` should preppend with `underline`
185:    function toString(uint256 value) internal pure returns (string memory) {
211:    function toHexString(uint256 value) internal pure returns (string memory) {
227:    function toHexString(uint256 value, uint256 length) internal pure returns (string memory) {
457:    function isContract(address account) internal view returns (bool) {
485:    function sendValue(address payable recipient, uint256 amount) internal {
510:    function functionCall(address target, bytes memory data) internal returns (bytes memory) {
520:    function functionCall(
539:    function functionCallWithValue(
553:    function functionCallWithValue(
572:    function functionStaticCall(address target, bytes memory data) internal view returns (bytes memory) {
582:    function functionStaticCall(
599:    function functionDelegateCall(address target, bytes memory data) internal returns (bytes memory) {
609:    function functionDelegateCall(
1382:    function vaultValidated(uint256 vaultId) internal view returns (bool) {
1388:    function identityValidated(uint256 identityId) internal view returns (bool) {
1394:    function itemCacheValidated(uint256 itemCacheId) internal view returns (bool) {
1400:    function whitelistValidated(string memory vaultTokenId, uint256 leaderboardSpot,uint256 index, bytes32[] memory proof) internal view returns (bool) {
1451:    function toString(uint256 value) internal pure returns (string memory) {
```

https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/s1/NTItems.sol

```solidity
// private and internal `functions` should preppend with `underline`
185:    function toString(uint256 value) internal pure returns (string memory) {
211:    function toHexString(uint256 value) internal pure returns (string memory) {
227:    function toHexString(uint256 value, uint256 length) internal pure returns (string memory) {
457:    function isContract(address account) internal view returns (bool) {
485:    function sendValue(address payable recipient, uint256 amount) internal {
510:    function functionCall(address target, bytes memory data) internal returns (bytes memory) {
520:    function functionCall(
539:    function functionCallWithValue(
553:    function functionCallWithValue(
572:    function functionStaticCall(address target, bytes memory data) internal view returns (bytes memory) {
582:    function functionStaticCall(
599:    function functionDelegateCall(address target, bytes memory data) internal returns (bytes memory) {
609:    function functionDelegateCall(
1524:    function specialItemCheck(uint256 spotOnLeaderboard) internal
1557:    function boxValidated(uint256 boxId) internal view returns (bool) {
1563:    function whitelistValidated(string memory vaultTokenId, uint256 leaderboardSpot,uint256 index, bytes32[] memory proof) internal view returns (bool) {
1610:    function toString(uint256 value) internal pure returns (string memory) {

// private and internal `variables` should preppend with `underline`
1272:    string[] private weapon = [
1301:    string[] private apparel = [
1361:    string[] private vehicle = [
1381:    string[] private helm = [
```

https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/s1/NTCitizenDeploy.sol

```solidity
// private and internal `functions` should preppend with `underline`
205:    function toString(uint256 value) internal pure returns (string memory) {
231:    function toHexString(uint256 value) internal pure returns (string memory) {
247:    function toHexString(uint256 value, uint256 length) internal pure returns (string memory) {
477:    function isContract(address account) internal view returns (bool) {
505:    function sendValue(address payable recipient, uint256 amount) internal {
530:    function functionCall(address target, bytes memory data) internal returns (bytes memory) {
540:    function functionCall(
559:    function functionCallWithValue(
573:    function functionCallWithValue(
592:    function functionStaticCall(address target, bytes memory data) internal view returns (bytes memory) {
602:    function functionStaticCall(
619:    function functionDelegateCall(address target, bytes memory data) internal returns (bytes memory) {
629:    function functionDelegateCall(
1375:    function rewardRateByTokenId(uint256 tokenId) internal view returns(uint256){
1405:    function calculateCitizenReward(uint256 identityId, uint256 vaultId) internal view returns(uint256){
1539:    function vaultValidated(uint256 vaultId) internal view returns (bool) {
1544:    function identityValidated(uint256 identityId) internal view returns (bool) {
1557:    function itemCacheValidated(uint256 itemCacheId) internal view returns (bool) {
1562:    function landDeedValidated(uint256 landDeedId) internal view returns (bool) {
1639:    function toString(uint256 value) internal pure returns (string memory) {
```

https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/s1/BYTESContract.sol

```solidity
// private and internal `functions` should preppend with `underline`
55:    function isContract(address account) internal view returns (bool) {
82:    function sendValue(address payable recipient, uint256 amount) internal {
108:    function functionCall(address target, bytes memory data) internal returns (bytes memory) {
118:    function functionCall(address target, bytes memory data, string memory errorMessage) internal returns (bytes memory) {
133:    function functionCallWithValue(address target, bytes memory data, uint256 value) internal returns (bytes memory) {
143:    function functionCallWithValue(address target, bytes memory data, uint256 value, string memory errorMessage) internal returns (bytes memory) {
297:    function add(uint256 a, uint256 b) internal pure returns (uint256) {
314:    function sub(uint256 a, uint256 b) internal pure returns (uint256) {
328:    function sub(uint256 a, uint256 b, string memory errorMessage) internal pure returns (uint256) {
345:    function mul(uint256 a, uint256 b) internal pure returns (uint256) {
371:    function div(uint256 a, uint256 b) internal pure returns (uint256) {
387:    function div(uint256 a, uint256 b, string memory errorMessage) internal pure returns (uint256) {
407:    function mod(uint256 a, uint256 b) internal pure returns (uint256) {
423:    function mod(uint256 a, uint256 b, string memory errorMessage) internal pure returns (uint256) {
879:    function validateIdentity(uint256 tokenId) internal view returns(bool)
```

---

### Version [5]

- Pragma versions should be standardized and avoid floating pragma `( ^ )`.

https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/NeoTokyoStaker.sol

```solidity
// avoid floating pragma `^` and make all versions uniform.
2:    pragma solidity ^0.8.19;
```

https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/BYTES2.sol

```solidity
// avoid floating pragma `^` and make all versions uniform.
2:    pragma solidity ^0.8.19;
```

https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/s2/NTS2LandDeploy.sol

```solidity
// avoid floating pragma `^` and make all versions uniform.
10: pragma solidity ^0.8.0;
```

https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/s2/NTS2Items.sol

```solidity
// avoid floating pragma `^` and make all versions uniform.
10: pragma solidity ^0.8.0;
```

https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/s2/NTOuterIdentity.sol

```solidity
// avoid floating pragma `^` and make all versions uniform.
10:   pragma solidity ^0.8.0;
```

https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/s2/NTOuterCitizenDeploy.sol

```solidity
// make all versions uniform.
10: pragma solidity 0.8.11;
```

https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/s1/vaultBox.sol

```solidity
// avoid floating pragma `^` and make all versions uniform.
10:   pragma solidity ^0.8.0;
```

https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/s1/beckLoot.sol

```solidity
// avoid floating pragma `^` and make all versions uniform.
10:   pragma solidity ^0.8.0;
```

https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/s1/NTLandDeploy.sol

```solidity
// avoid floating pragma `^` and make all versions uniform.
10:   pragma solidity ^0.8.0;
```

https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/s1/NTItems.sol

```solidity
// avoid floating pragma `^` and make all versions uniform.
10:   pragma solidity ^0.8.0;
```

https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/s1/NTCitizenDeploy.sol

```solidity
// make all versions uniform.
10: pragma solidity 0.8.11;
```

https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/s1/BYTESContract.sol

```solidity
// avoid floating pragma `^` and make all versions uniform.
5:  pragma solidity ^0.8.0;
```

https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/interfaces/IStaker.sol

```solidity
// avoid floating pragma `^`
2:  pragma solidity ^0.8.19;
```

https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/interfaces/IGenericGetter.sol

```solidity
// avoid floating pragma `^`
2:  pragma solidity ^0.8.19;
```

https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/interfaces/IByteContract.sol

```solidity
// avoid floating pragma `^`
2:  pragma solidity ^0.8.19;
```

https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/access/PermitControl.sol

```solidity
// avoid floating pragma `^`
2:  pragma solidity ^0.8.19;
```


---


### Observations [6]

https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/s1/vaultBox.sol

```solidity
// misspell
1293:        "Exectuive",
```

https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/s1/beckLoot.sol

```solidity
// misspell
1288:        "Exectuive",

// misspell?
1325:        "Athele",
```

https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/s2/NTS2LandDeploy.sol

```solidity
// there is a mix of styling with opening brackets on the same line and on the next one. it's good practice to open on all on the same line13

// public visibility should come before override as a good practice. example `public view override return`
1303:    function tokenURI(uint256 tokenId) override public view returns (string memory) {
```

https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/s2/NTS2Items.sol

```solidity
// there is a mix of styling with opening brackets on the same line and on the next one. it's good practice to open on all on the same line13

// public visibility should come before override as a good practice. example `public view override return`
1328:    function tokenURI(uint256 tokenId) override public view returns (string memory) {
```

https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/s2/NTOuterIdentity.sol

```solidity
// there is a mix of styling with opening brackets on the same line and on the next one. it's good practice to open on all on the same line


// public visibility should come before override as a good practice. example `public view override return`
1448:    function tokenURI(uint256 tokenId) override public view returns (string memory) {
```


https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/s2/NTOuterCitizenDeploy.sol

```solidity
// public visibility should come before override as a good practice. example `public view override return`
1332:    function tokenURI(uint256 tokenId) override public view returns (string memory) {
```

https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/s1/beckLoot.sol

```solidity
// public visibility should come before override as a good practice. example `public view override return`
1477:    function tokenURI(uint256 tokenId) override public view returns (string memory) {
```

https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/s1/NTLandDeploy.sol

```solidity
// public visibility should come before override as a good practice. example `public view override return`
1327:    function tokenURI(uint256 tokenId) override public view returns (string memory) {
```

https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/s1/NTItems.sol

```solidity
// public visibility should come before override as a good practice. example `public view override return`
1492:    function tokenURI(uint256 tokenId) override public view returns (string memory) {
```

https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/s1/NTItems.sol

```solidity
// there is a mix of styling with opening brackets on the same line and on the next one. it's good practice to open on all on the same line
```

https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/s1/BYTESContract.sol

```solidity
// the code needs a better indentation, also there is a mix of styling with opening brackets on the same line and on the next one. Here it is few examples
799:	using SafeMath for uint256;
817:	constructor() {
810:	ICitizen public citizenContract;
...
```