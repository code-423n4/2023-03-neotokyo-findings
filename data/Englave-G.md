G01. Inefficient `_stringEquals` function
The `_stringEquals` function currently in use contains excessive lines of code, which makes it more difficult to comprehend and maintain. Additionally, it consumes more gas than a simpler alternative. It would be beneficial to implement a more streamlined version:
```
function _stringEquals (string memory _a, string memory _b) public returns (bool) {
     return keccak256(bytes(_a)) == keccak256(bytes(_b));
 }
```
The existing function uses Gas:
1948  if strings have different lengths
2123 in case they have similar lengths, but strings are not equals
2107 if they are equals.

The proposed solution uses Gas:
1931 for all specified cases above, so it is more efficient in any highlighted case.