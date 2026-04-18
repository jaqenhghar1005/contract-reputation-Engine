// SPDX-License-Identifier: MIT
pragma solidity ^0.8.20;

import "@openzeppelin/contracts/access/Ownable.sol";

contract ReputationEngine is Ownable {

    struct UserReputation {
        uint256 score;
        uint256 lastUpdated;
    }

    mapping(address => UserReputation) public reputations;

    event ReputationUpdated(address indexed user, uint256 newScore);

    constructor() Ownable(msg.sender) {}

    function increaseReputation(address user, uint256 amount) external onlyOwner {
        UserReputation storage rep = reputations[user];
        rep.score += amount;
        rep.lastUpdated = block.timestamp;

        emit ReputationUpdated(user, rep.score);
    }

    function decreaseReputation(address user, uint256 amount) external onlyOwner {
        UserReputation storage rep = reputations[user];

        if (rep.score >= amount) {
            rep.score -= amount;
        } else {
            rep.score = 0;
        }

        rep.lastUpdated = block.timestamp;

        emit ReputationUpdated(user, rep.score);
    }

    function getScore(address user) external view returns (uint256) {
        return reputations[user].score;
    }
}
