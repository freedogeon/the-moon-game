# Algodetial

The player finally decides to run at x + x_delta when he sees the curve to x

```
The odds

0.99 / [1 - (x + x_delta)]

Conditional probability

[1- (x+x_delta)] / [1- x]
=
(1-x-x_delta)/(1-x)
=
1- x_delta/(1- x)

Conditions for EV value
=
Probability * conditional probability
=
0.99/[1- (x+x_delta)] * [1-x_delta/(1-x)]
=

EV value = conditional EV value * the probability that the player sees x
=
0.99 / [1 - (x + x_delta)] * [1 - x_delta/(1 - x)] * (1 - x)
=
0.99/(1-x-x_delta) * (1-x-x_delta)
=
0.99
```

# Verification

To prove our fairness we have generated a chain of 100,000,000 SHA256 hashes where each hash is the hash of the hexadecimal representation of the previous hash.

The formula for generating the game result: 
```javascript
//hashChain is the chain of 100,000,000 SHA256 hashes which are already generated.
//get the last hash of the chain
const gameHash = hashChain.pop();

const crashpoint = gameResult(gameHash);

//update the hash is used
hashChain.update({
  gameHash: gameHash,
  used: true
})

function gameResult(seed) {
  const nBits = 52; // number of most significant bits to use
  // 1. r = 52 most significant bits
  seed = seed.slice(0, nBits / 4);
  const r = parseInt(seed, 16);
  // 2. X = r / 2^52
  let X = r / Math.pow(2, nBits); // uniformly distributed in [0; 1)
  // 3. X = 99 / (1-X)
  X = 99 / (1 - X);
  // 4. return max(trunc(X), 100)
  const result = Math.floor(X);
  return Math.max(1, result / 100);
};
```

To verify our fairness just run the below function in order to generate the previous hash and crash point

```javascript
function generatePrevHash(prevHash, length=10){
  for (let i = 0; i < length; i++) {
    let hash = String(CryptoJS.SHA256(String(prevHash))); //We're using the CryptoJS javascript library https://www.npmjs.com/package/crypto-js
    let bust = gameResult(hash); //the gameResult calculation function above
    console.log(`HASH: ${hash}, crash point: ${bust}, index: ${i}`); //printing the result to the console
    prevHash = hash;
  }
}
```
