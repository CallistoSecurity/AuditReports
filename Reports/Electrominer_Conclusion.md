# [Electrominer Crowdsale](https://etherscan.io/address/0xb21d9c8e5db68e9c62a04fd3fbc459b1aa6c784a#code) security audit report performed by [Callisto Security Audit Department](https://github.com/EthereumCommonwealth/Auditing)

# 1. Conclusion:

No critical issue, smart contract is safe to deploy.

# 2. High severity issues:

No High severity issues

# 3. Medium severity issues:

No Medium severity issues

# 4. Low severity issues:

## 4.1. Owner Permissions

### Severity: low.

### Description

The contract owner has multiple permissions:

- set ETH/USD rate.
- ICO stages can be restarted at will, even after the end of stage is called.
- Send tokens from the allocated crowdsale tokens.

Multiple possible issues can be raised:
- Hack of private keys.
- Price manipulation.
- Stages reopening.
- etc ...

Investors and developers should be aware of such practice and be informed about the risks.

### Code snippet

https://gist.github.com/RideSolo/25ebbfa61fea3c8f49e107ab961ed6d2#file-electrominer-sol-L601#L604

https://gist.github.com/RideSolo/25ebbfa61fea3c8f49e107ab961ed6d2#file-electrominer-sol-L514#L543

https://gist.github.com/RideSolo/25ebbfa61fea3c8f49e107ab961ed6d2#file-electrominer-sol-L245#L290


# 5. Minor observation:

## 5.1. Extra if statement. 

### Description

[`If statement`](https://gist.github.com/yuriy77k/771837a9b54d81157a27496d50fff1bb#file-electrominer-sol-L346) is extra, because there is [`require`](https://gist.github.com/yuriy77k/771837a9b54d81157a27496d50fff1bb#file-electrominer-sol-L323) that will check this condition.

### Recommendation

`If statement` could be deleted.

## 5.2. Crowdsale Hard Cap

### Description

The crowdsale hard cap is set to be 50M USD (as per the white paper), with a total of 70000000 on sale and a price of 0.7142 USD per token (as per the white paper) the hard cap will be almost 50M.
In general hard cap can not be reached if the tokens are not all sold, however here, since the bonuses are also distributed from the 70M tokens allowed for the crowdsale only ~37.19M USD will be collected (if the ETH/USD rate is set frequently and all the tokens are sold).

### Code snippet

https://gist.github.com/RideSolo/25ebbfa61fea3c8f49e107ab961ed6d2#file-electrominer-sol-L137#L142

https://gist.github.com/RideSolo/25ebbfa61fea3c8f49e107ab961ed6d2#file-electrominer-sol-L162#L167

https://gist.github.com/RideSolo/25ebbfa61fea3c8f49e107ab961ed6d2#file-electrominer-sol-L451#L482

https://gist.github.com/RideSolo/25ebbfa61fea3c8f49e107ab961ed6d2#file-electrominer-sol-L355#L360



# Revealing audit reports:

https://gist.github.com/yuriy77k/851f4d362e99326e368e49f52cf71af6

https://gist.github.com/yuriy77k/72ae248dff3b9bcfbeb98965086be27d

https://gist.github.com/yuriy77k/ab41d3c27a7f6cd9d5409a209ddb61d2
