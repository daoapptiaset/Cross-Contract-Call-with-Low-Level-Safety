# Cross-Contract-Call-with-Low-Level-Safety
// SPDX-License-Identifier: MIT
pragma solidity ^0.8.20;

contract SafeCaller {
    error CallFailed();

    function safeCall(address target, bytes memory data) internal returns (bytes memory) {
        (bool success, bytes memory result) = target.call(data);
        if (!success) {
            if (result.length > 0) {
                assembly {
                    let returndata_size := mload(result)
                    revert(add(32, result), returndata_size)
                }
            }
            revert CallFailed();
        }
        return result;
    }
}
