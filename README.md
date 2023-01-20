# Danki's Smart Contract Auditing Framework
Billions of dollars worth of crypto have been lost due to faulty smart contracts.

Beyond devastating those who lose funds, these hacks harm the ecosystem as a whole by signaling the immaturity of the technology and reinforcing people’s fears about it. A thorough audit could have prevented many of these issues, saving a ton of headaches, money, and stifled progress.

So for the public good, Danki compiled this simple guide to smart contract auditing after hours and hours of studying security experts in the blockchain space like Mudit Gupta, Trail of Bits, Samczsun, and OpenZeppelin. Also special thanks to CryptoFinLabs for helping me create a good layout as well as examples of core checks and resiliency checks for this framework.

## A Bird's Eye View of the Audit
Here are the steps I take when presented with a smart contract:
- [ ] Read the documentation and non-code resources
- [ ] Skim to understand the smart contract's architecture
- [ ] Create a threat model
- [ ] Look at places that can do value exchange
- [ ] Run your core checks
- [ ] Do a line-by-line review
- [ ] Do another review from the perspective of every actor in your threat model
- [ ] Run automated auditing tools
- [ ] Test the contract's resilience
- [ ] Pore over the unit tests and check the code coverage
 
## Checklist 
### Read the documentation and non-code resources
It is important to first read the documentation, watch the demo (if there's any), or check their website to fully understand how the program is intended to work. Read on how each functionality is supposed to work in the documents so that you can identify when there are mismatches and possibly overlooked consequences on how the contract's functions are written.

### Understand the smart contract architecture
Check how the functionalities and contracts interact with each other. You can plot a dependency graph with tools like Surya. Once you have you good understanding of the usual flow of value and the checks within the contracts, you now can...

### Create a threat model
Have a good list of the following:
- [ ] System actors
- [ ] Possible attack vectors
- [ ] Common pitfalls and past exploits for the type of contract that you're working on
- [ ] Missing checks

### Look at places that can do value exchange
Critical contract vulnerabilities usually involve functions where there is an exchange of value. So closely look at places in the contract where there is:
- [ ] transfer
- [ ] transferFrom
- [ ] send
- [ ] call
- [ ] delegatecall
- [ ] selfdestruct
- [ ] inline assembly code

### Run your core checks
These are just some basic checks that the Danki runs in any contract. There are more of them, so feel free to have your own list. This can change as solidity evolves, but if you want ideas, you can find an exhaustive list in the SWC Registry:

- [ ] Solidity version must be the same across all contracts. Preferably they use v.0.8.0 or better
- [ ] There should be no superuser privileges. Use a multisig wallet and possibly a timelock to call critical functions inside the contract.
- [ ] Function Visibility
  - [ ] Ensure that all relevant functions are marked with the correct visibility
- [ ] Reentrancy
  - [ ] Ensure that all relevant functions are guarded against reentrancy attacks
- [ ] Oracles
  - [ ] Check if the oracles they use can be manipulated in any way
- [ ] Dependencies
  - [ ] Use audited and trustworthy dependencies
  - [ ] Ensure newly written code is minimized by using libraries
- [ ] Time Manipulation - Timestamps can theoretically be manipulated by malicious miners by up to a few minutes
  - [ ] Ensure important mechanisms aren't overly sensitive to timestamps
- [ ] Rounding Errors
  - [ ] Check that truncation doesn't produce unexpected behavior (eg. incorrect results, locked funds)
- [ ] Randomness
  - [ ] Don't rely on pseudo-randomness for important mechanisms (eg. keccak with a deterministic seed like blockhash, blocknumber, etc.)
- [ ] Validate inputs of external/public functions
  - [ ] Ensure requires to bound and check presence of arguments
- [ ] Prevent unbounded loops
- [ ] Appropriate use of push payments
- [ ] Change old Solidity constructs
  - [ ] selfdestruct vs suicide
  - [ ] keccak256 vs sha3
- [ ] Don't use tx.origin as an authentication mechanism

### Do a line-by-line review
This is possibly the most time-intensive part as it is done purely manually. Make sure that all the contract logic match with the specs.

### Do another review from the perspective of every actor in your threat model
It is very important to see from the perspective of its users:
- [ ] What are their motivations in using the protocol?
- [ ] What are their goals?
- [ ] Are there potential exploits that can be easily seen when you're in the actor's shoes?

### Test the contract's resilience
We always check for code that will mitigate risk when (not if) a contract fails. When a contract doesn’t have this, it’s often a warning sign.
Auditing helps catch many bugs, but shouldn’t also be seen as a magic bullet. Your system still needs to handle failure gracefully.

- [ ] What failure states would be most disastrous?
- [ ] Are there assert checks for critical values? (e.g., individual balances total to sum)
- [ ] Speed Bumps
- [ ] Does the contract have a speed bump? (e.g., delay in withdrawing funds, like the DAO)
- [ ] Circuit Breakers
  - [ ] Does the contract have a circuit breaker? (preventing critical functions in an emergency mode)

### Run automated auditing tools
Some automated tests you can run include but are not limited to:

- [ ] Slither for basic checks
- [ ] Fuzzing with Echidna, Foundry, or similar tools
- [ ] Formal Verification (time and resource intensive, but great for short but critical contracts)

### Pore over the unit tests and check the code coverage
- [ ] Test Coverage
  - [ ] Have 100% branch test coverage
- [ ] Unit Tests
  - [ ] Cover all critical edge cases with unit tests
- [ ] Integration Tests
  - [ ] Have extensive integration tests
- [ ] Code Freeze
  - [ ] Don't deploy recently written code, especially when written under a tight deadline

### Where to go next
If you are new to all this and wanted to learn more about smart contract security, or you have a project and just wanted to make sure your contracts are audit-ready, you can read more through these resources:

- OpenZeppelin's Smart Contract Audit Readiness Guide: https://learn.openzeppelin.com/security-audits/readiness-guide
- OpenZeppelin's Ethernaut Challenge: https://ethernaut.openzeppelin.com
- Smart Contract Weaknesses and Classifications: https://swcregistry.io/