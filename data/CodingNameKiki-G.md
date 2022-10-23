1. Save a storage variable's reference instead of repeatedly fetching it from storage.

Declare a storage type variable and use it instead of repeatedly fetching the reference in a map or an array.

https://gist.github.com/CodingNameKiki/508fe272fa7025927d40de3369ea828a

2. Using calldata instead of memory for read-only arguments in external functions

When a function with a memory array is called externally, the abi.decode() step has to use a for-loop to copy each index of the calldata to the memoryindex. Each iteration of this for-loop costs at least 60 gas (i.e. 60 * <mem_array>.length). Using calldata directly, obliviates the need for such a loop in the contract code and runtime execution. Structs have the same overhead as an array of length one

When arguments are read-only on external functions, the data location should be calldata:

https://gist.github.com/CodingNameKiki/f1d4dbc1e52a3156fd077686f1358ba1

3. Byte constants are cheaper than string constants

Consider using bytes32, when the data can fit into this 32 bytes. It will be much cheaper than using string.

https://gist.github.com/CodingNameKiki/2f44a67a21a4ee31d10d947fdcf8f468



