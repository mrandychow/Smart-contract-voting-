// SPDX-License-Identifier: MIT
pragma solidity ^0.8.0;

contract VotingContract {
    struct Topic {
        string title;
        string description;
    }
    
    address public admin;
    Topic[] public topics;
    
    mapping(uint => mapping(address => bool)) public hasVoted;
    mapping(address => uint) public scores;
    
    event TopicCreated(uint topicId, string title);
    event TopicCancelled(uint topicId, string title);
    event VoteCasted(uint topicId, string title, address voter);
    
    modifier onlyAdmin() {
        require(msg.sender == admin, "Only admin can perform this action");
        _;
    }
    
    constructor() {
        admin = msg.sender;
    }
    
    function createTopic(string memory _title, string memory _description) public onlyAdmin {
        uint topicId = topics.length;
        topics.push(Topic(_title, _description));
        
        emit TopicCreated(topicId, _title);
    }
    
    function cancelTopic(uint _topicId) public onlyAdmin {
        require(_topicId < topics.length, "Invalid topic ID");
        string memory title = topics[_topicId].title;
        
        delete topics[_topicId];
        
        emit TopicCancelled(_topicId, title);
    }
    
    function vote(uint _topicId) public {
        require(_topicId < topics.length, "Invalid topic ID");
        require(!hasVoted[_topicId][msg.sender], "Already voted");
        
        hasVoted[_topicId][msg.sender] = true;
        scores[msg.sender]++;
        
        emit VoteCasted(_topicId, topics[_topicId].title, msg.sender);
    }
}
