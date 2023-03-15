\[M-01\] No access control for `getReward` will allow an attacker to claim rewards on behalf of a user

who may not be willing to do so

```plaintext
    function getReward(address _to) external {
```

Recommended Mitigation Steps: 

Consider adding access control to the function:

```plaintext
    function getReward(address _to) external hasValidPermit(UNIVERSAL, ADMIN) {
```