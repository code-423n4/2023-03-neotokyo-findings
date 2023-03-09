# Require burning of BYTES tokens to be more than 0

On [2023-03-neotokyo/contracts/staking/BYTES2.sol#L140](https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/BYTES2.sol#L140) the function **burn**:

```Solidity
function burn (
    address _from,
    uint256 _amount
) hasValidPermit(UNIVERSAL, BURN) external {
    _burn(_from, _amount);

    /*
    We are aware that this math does not round perfectly for all values of
    `_amount`. We don't care.
    */
    uint256 treasuryShare;
    unchecked {
        treasuryShare = _amount * 2 / 3;
    }
    _mint(TREASURY, treasuryShare);
}
```
Allows to enter 0 amount to the function, this technically doesn't affect either the first burn that it's done neither the minting at the end. 

## Impact
The reason it's suggested to require more than 0 amount of BYTES tokens is for accountability and monitoring purposes. 

An attacker might be trying to get into the BURN permitted addresses and it could use this function to test any sort of exploit.

Requiring more than 0 amount to call this function would not prevent a sucessful exploitation of getting into the BURN permitted addresses but it would bring eyes into the matter by forcing the attacker to change any amount of burned/minted tokens thus alerting the team or the community about an ongoing attack.

## Recommendations

There are 2 recommendations that would bring a better QA to the code and both can be applied simultaneously:

**Emiting a burning event**

Both burn and mint functions fromt the ERC20 contract create a transfer function, this can be easily overlooked if the purpose to stop an attack like this is to monitor valuable Events.

By emitting an event such as:
```
emit BurnBYTES(_from, _amount);
```
This function could be more easily monitored thus increasing the possibility to respond to any attack on it.


**Requiring the _amount to be more than 0**

If burn function would be changed to this: 
```
function burn (
    address _from,
    uint256 _amount
) hasValidPermit(UNIVERSAL, BURN) external {
    if(_amount > 0){
        _burn(_from, _amount);

        /*
        We are aware that this math does not round perfectly for all values of
        `_amount`. We don't care.
        */
        uint256 treasuryShare;
        unchecked {
            treasuryShare = _amount * 2 / 3;
        }
        _mint(TREASURY, treasuryShare);
        emit BurnBYTES(_from, _amount);
    }
}
```

This refactored code would not allow the attacker to try and burn 0 tokens to test its permissions nor would provide any feedback of failure or success if the attacker doesn't send more than 0 amount of BYTES tokens, achieving this alerting mechanism if something bad is happening (plus the BurnBYTES event is emitted too).