# Introduction to Testing Using `snforge`
This branch introduces the fundamental knowledge needed for getting started with writing tests for starknet contracts using `snforge` (by [Starket Foundry](https://foundry-rs.github.io/starknet-foundry/index.html))

## Getting Started
It contains some sample programs and it's associated tests.

### Set up
To run the tests, make sure you have Starknet Foundry installed and you can use `snforge` from your terminal.
- To verify if you have Starknet Foundry and can use `snforge` from your terminal, enter this command:
```bash
snforge --version
```
If you don't have starknet foundry installed, follow the instruction from the official documentation [here](https://foundry-rs.github.io/starknet-foundry/index.html)

### Running Tests
- To run all the tests in the project, enter the following command in your terminal:
```bash
snforge test
```
- The above command will first build all the Starknet contracts to check for errors, and afterwards proceed to run all tests it can find in the project.
- Before running the command, please ensure your project builds successfully by running:
```bash
scarb build
```


## Important Cheat Codes
`prank` - changes the caller address for contracts, for a number of calls. Calling a cheatcode with CheatSpan::TargetCalls(N) is going to activate the cheatcode for N calls to a specified CheatTarget, after which it's going to be automatically canceled. Of course the cheatcode can still be canceled before its CheatSpan goes down to 0 - simply call stop_prank on the target manually.
``` =shell
prank(CheatTarget::One(contract_address), new_caller_address, CheatSpan::TargetCalls(1))
```

`start_prank` - changes the caller address for contracts
``` =shell
    // Change the caller address to user_1 before calling increase_count
    start_prank(CheatTarget::One(contract_address), user_1_addr);
```

`stop_prank` - cancels the prank / start_prank for contracts
``` =shell
// The address when calling contract at the `contract_address` address will no longer be changed
stop_prank(CheatTarget::One(contract_address));
```


---
### Error Handling 
Some tests are expected fail. This is useful when you want to verify that an action fails as expected. The following methodologies can be deployed to 
1. Using `#[should_panic(expected: ('panic message', ))]` attribute

2. Using `SafeDispatcher`, we can test that the function in fact panics with an expected message.
To utilize `SafeDispatcher`, it's required to annotate its usage with `#[feature("safe_dispatcher")]`
---

### Events
#### Asserting events manually
- After contract deployment we created the spy with spy_events cheatcode. From this moment all events emitted by the SpyEventsChecker contract will be spied.
- We have to call fetch_events method on the created spy to load emitted events into it.
- When events are fetched they are loaded into the events property of our spy, and we can assert them.
- If the event is emitted by calling self.emit method, its hashed name is saved under the keys.at(0) (this way Starknet handles events)
- It is worth noting that when we call the method which emits an event, spy is not updated immediately.



#### Asserting emission with `assert_emitted` method
- After the contract is called, we don't have to call fetch_events on the spy (it is done inside the assert_emitted method).
- `assert_emitted` takes the array snapshot of tuples (ContractAddress, event) we expect were emitted.
- After the assertion, found events are removed from the spy. It stays clean and ready for the next events.

