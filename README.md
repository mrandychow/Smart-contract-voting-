

// SPDX-License-Identifier: MIT
pragma solidity ^0.8.0;

contract VotingContract {
    struct Topic {
        string title;
        string description;
        bool isOpen;
    }
    
    address public admin;
    Topic[] public topics;
    mapping(uint => mapping(address => bool)) public hasVoted;
    
    mapping(address => uint) public scores;
    mapping(address => bool) public hasClaimedReward;
    
    event TopicCreated(uint topicId, string title);
    event TopicClosed(uint topicId, string title);
    event VoteCasted(uint topicId, string title, address voter);
    event RewardClaimed(address voter, uint score);
    
    modifier onlyAdmin() {
        require(msg.sender == admin, "Only admin can perform this action");
        _;
    }
    
    constructor() {
        admin = msg.sender;
    }
    
    function createTopic(string memory _title, string memory _description) public onlyAdmin {
        uint topicId = topics.length;
        topics.push(Topic(_title, _description, true));
        
        emit TopicCreated(topicId, _title);
    }
    
    function closeTopic(uint _topicId) public onlyAdmin {
        require(_topicId < topics.length, "Invalid topic ID");
        require(topics[_topicId].isOpen, "Topic is already closed");
        
        topics[_topicId].isOpen = false;
        
        emit TopicClosed(_topicId, topics[_topicId].title);
    }
    
    function vote(uint _topicId) public {
        require(_topicId < topics.length, "Invalid topic ID");
        require(topics[_topicId].isOpen, "Topic is closed");
        require(!hasVoted[_topicId][msg.sender], "Already voted");
        
        hasVoted[_topicId][msg.sender] = true;
        scores[msg.sender]++;
        
        emit VoteCasted(_topicId, topics[_topicId].title, msg.sender);
    }
    
    function claimReward() public {
        require(scores[msg.sender] > 0, "No score to claim");
        require(!hasClaimedReward[msg.sender], "Reward already claimed");
        
        // Transfer ERC20 tokens as reward
        // Replace this with the actual ERC20 token transfer logic
        // ...
        
        hasClaimedReward[msg.sender] = true;
        
        emit RewardClaimed(msg.sender, scores[msg.sender]);
    }
    
    function hasUserVoted(uint _topicId, address _voter) public view returns (bool) {
        require(_topicId < topics.length, "Invalid topic ID");
        return hasVoted[_topicId][_voter];
    }
}
