// SPDX-License-Identifier: MIT
pragma solidity ^0.8.20;

import "@openzeppelin/contracts/token/ERC20/IERC20.sol";

contract RedoDex {
    IERC20 public token1;
    IERC20 public token2;
    uint public reserve1;
    uint public reserve2;

    constructor(address _token1, address _token2) {
        token1 = IERC20(_token1);
        token2 = IERC20(_token2);
    }

    // Basic Swap Function
    function swap(address _tokenIn, uint _amountIn) external returns (uint amountOut) {
        bool isToken1 = _tokenIn == address(token1);
        (IERC20 tokenIn, IERC20 tokenOut, uint resIn, uint resOut) = isToken1 
            ? (token1, token2, reserve1, reserve2) 
            : (token2, token1, reserve2, reserve1);

        tokenIn.transferFrom(msg.sender, address(this), _amountIn);

        // Constant Product Formula: (x + dx) * (y - dy) = x * y
        // dy = (y * dx) / (x + dx)
        amountOut = (resOut * _amountIn) / (resIn + _amountIn);
        
        tokenOut.transfer(msg.sender, amountOut);
        _updateReserves();
    }

    function _updateReserves() private {
        reserve1 = token1.balanceOf(address(this));
        reserve2 = token2.balanceOf(address(this));
    }
}# redodex
