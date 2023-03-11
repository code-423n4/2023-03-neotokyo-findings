**BYTES2.sol**
- L150/151/154 - It ends up being more comfortable to visualize and execute gas costs directly _mint(TREASURY, _amount * 2 / 3);


**NeoTokyoStaker.sol**
- L631/632/638/640/664/666/903/904/939/943/969/948/949/950/962/963/966/969/971/977/1016/1017/1022/1023/1140/
1159/1153/1164/1220/1221/1280/1281/1282/1289/1290/1292/1342/1343/1355/1357/1378/1379 - 
A variable is created in memory that will only be used once, therefore it could be avoided and used directly where the operation is necessary.

- L1823/1824/1825/1830/1831/1834/1837 - Within the for loop, the _inputs[i] parameter is used multiple times, therefore, in order not to go through each line and search for i, it is less expensive to create a variable in memory.
The same thing happens with _inputs[i].assetType and _inputs[i].rewardWindows[j], therefore other variables could be created in memories with these parameters.
 
