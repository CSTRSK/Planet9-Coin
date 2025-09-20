# Planet9-Coin
# Project Planet 9 Coin - Technical Specification

**Author**: CSTRSK
**Version**: 1.0  
**Date**: September 2025  
**Project Type**: HTML5-based Cryptocurrency

---

## Table of Contents

1. [Executive Summary](#executive-summary)
2. [System Architecture](#system-architecture)
3. [Front-End Specification](#front-end-specification)
4. [Back-End Specification](#back-end-specification)
5. [Database Schema](#database-schema)
6. [API Documentation](#api-documentation)
7. [Cryptographic Implementation](#cryptographic-implementation)
8. [Pseudocode](#pseudocode)
9. [Development Phases](#development-phases)
10. [Security Considerations](#security-considerations)
11. [Deployment Guidelines](#deployment-guidelines)

---

## Executive Summary

Project Planet 9 Coin is a modernized, educational cryptocurrency system inspired by Duino-Coin. The project emphasizes accessibility, education, and fun while maintaining cryptographic security. The system features a centralized architecture for simplified management, but incorporates tamper-evident ledger technology for transparency and trust.

### Key Features
- HTML5-based browser mining with Web Worker implementation
- Dual algorithm support (SHA-256 and SHA1)
- Kolka reward system for fair distribution
- Tamper-evident blockchain ledger
- Educational focus with hobbyist appeal
- Cross-platform compatibility

---

## System Architecture

### Centralized Design Philosophy

The system adopts a centralized architecture for the following benefits:
- **Simplified Management**: Easier maintenance and updates
- **Lower Operational Costs**: Reduced infrastructure complexity
- **Educational Focus**: Simplified concepts for learning
- **Rapid Development**: Faster iteration and feature deployment

### Trust and Transparency Layer

Despite centralization, the system implements:
- **Tamper-Evident Ledger**: Cryptographic audit trail
- **Public API**: Transparent data access
- **Open Source Design**: Community verification
- **Periodic Immutable Backups**: External verification points

### High-Level Architecture Diagram

```
┌─────────────────┐    ┌─────────────────┐    ┌─────────────────┐
│   HTML5 Miner   │◄──►│  PHP Backend    │◄──►│    Database     │
│   (Web Worker)  │    │  (API Server)   │    │  (PostgreSQL)   │
└─────────────────┘    └─────────────────┘    └─────────────────┘
         │                       │                       │
         │                       ▼                       │
         │              ┌─────────────────┐               │
         │              │ Immutable Store │               │
         │              │ (Cloud Storage) │               │
         │              └─────────────────┘               │
         │                                                │
         └────────────── Real-time Updates ──────────────┘
```

---

## Front-End Specification

### Technology Stack

- **HTML5**: Semantic markup and modern features
- **CSS3**: Responsive design and animations
- **Modern JavaScript (ES2020+)**: Async/await, modules, Web Workers
- **Web Crypto API**: Hardware-accelerated cryptography
- **WebSocket**: Real-time communication

### Multi-threaded Architecture

#### Main Thread Responsibilities
- User interface updates
- Real-time statistics display
- Network communication management
- User interaction handling

#### Web Worker Responsibilities
- Cryptographic hashing computations
- Nonce generation and validation
- Mining algorithm execution
- Background job processing

### File Structure
```
frontend/
├── index.html
├── css/
│   ├── main.css
│   ├── dashboard.css
│   └── responsive.css
├── js/
│   ├── main.js
│   ├── miner-worker.js
│   ├── crypto-utils.js
│   ├── api-client.js
│   └── ui-controller.js
└── assets/
    ├── images/
    └── icons/
```

### Web Worker Implementation

The mining logic runs in a dedicated Web Worker to prevent UI blocking:

```javascript
// miner-worker.js structure
class MiningWorker {
    constructor() {
        this.isMining = false;
        this.currentJob = null;
        this.hashCount = 0;
        this.startTime = null;
    }

    async startMining(job) {
        this.ismining = true;
        this.currentJob = job;
        this.startTime = Date.now();
        
        // Main mining loop
        while (this.isMining) {
            const nonce = this.generateNonce();
            const hash = await this.computeHash(job.data, nonce);
            
            if (this.validateHash(hash, job.difficulty)) {
                this.submitSolution(nonce, hash);
                break;
            }
            
            this.hashCount++;
            
            // Report progress every 1000 hashes
            if (this.hashCount % 1000 === 0) {
                this.reportProgress();
            }
        }
    }
}
```

### Cryptographic Implementation

#### Algorithm Selection Logic
```javascript
function selectHashingAlgorithm() {
    const deviceCapabilities = assessDeviceCapabilities();
    
    if (deviceCapabilities.powerLevel >= 'high') {
        return 'SHA-256';
    } else {
        return 'SHA1';
    }
}

function assessDeviceCapabilities() {
    const navigator = window.navigator;
    const hardwareConcurrency = navigator.hardwareConcurrency || 1;
    const memory = navigator.deviceMemory || 1;
    
    if (hardwareConcurrency >= 4 && memory >= 4) {
        return { powerLevel: 'high' };
    } else {
        return { powerLevel: 'low' };
    }
}
```

#### Web Crypto API Integration
```javascript
class CryptoHasher {
    static async sha256(data) {
        const encoder = new TextEncoder();
        const dataBuffer = encoder.encode(data);
        const hashBuffer = await crypto.subtle.digest('SHA-256', dataBuffer);
        return this.bufferToHex(hashBuffer);
    }
    
    static async sha1(data) {
        const encoder = new TextEncoder();
        const dataBuffer = encoder.encode(data);
        const hashBuffer = await crypto.subtle.digest('SHA-1', dataBuffer);
        return this.bufferToHex(hashBuffer);
    }
    
    static bufferToHex(buffer) {
        return Array.from(new Uint8Array(buffer))
            .map(b => b.toString(16).padStart(2, '0'))
            .join('');
    }
}
```

---

## Back-End Specification

### Technology Stack

- **Language**: PHP 8.2+
- **Framework**: Custom lightweight framework or Slim Framework
- **Database**: PostgreSQL 14+
- **Web Server**: Nginx with PHP-FPM
- **Cache**: Redis for session and temporary data
- **Queue**: Redis-based job queue for heavy operations

### Directory Structure
```
backend/
├── public/
│   └── index.php
├── src/
│   ├── Controllers/
│   │   ├── MiningController.php
│   │   ├── UserController.php
│   │   └── LedgerController.php
│   ├── Models/
│   │   ├── User.php
│   │   ├── Transaction.php
│   │   ├── Block.php
│   │   └── MiningJob.php
│   ├── Services/
│   │   ├── MiningService.php
│   │   ├── KolkaRewardService.php
│   │   ├── LedgerService.php
│   │   └── CryptoService.php
│   ├── Utils/
│   │   ├── MerkleTree.php
│   │   ├── HashValidator.php
│   │   └── DatabaseManager.php
│   └── Config/
│       ├── database.php
│       ├── mining.php
│       └── security.php
├── migrations/
├── tests/
├── composer.json
└── README.md
```

### Core Services

#### Mining Service
```php
class MiningService {
    private $kolkaService;
    private $ledgerService;
    
    public function generateJob($userId, $deviceCapability) {
        $difficulty = $this->kolkaService->calculateDifficulty($userId);
        $baseData = $this->generateBaseData();
        
        return [
            'job_id' => uniqid(),
            'user_id' => $userId,
            'data' => $baseData,
            'difficulty' => $difficulty,
            'algorithm' => $this->selectAlgorithm($deviceCapability),
            'timestamp' => time(),
            'expires_at' => time() + 300 // 5 minute expiry
        ];
    }
    
    public function validateSubmission($jobId, $nonce, $hash) {
        $job = $this->getJob($jobId);
        
        if (!$job || $job['expires_at'] < time()) {
            return ['valid' => false, 'reason' => 'Job expired'];
        }
        
        $expectedHash = $this->computeExpectedHash(
            $job['data'], 
            $nonce, 
            $job['algorithm']
        );
        
        if ($expectedHash !== $hash) {
            return ['valid' => false, 'reason' => 'Invalid hash'];
        }
        
        if (!$this->validateDifficulty($hash, $job['difficulty'])) {
            return ['valid' => false, 'reason' => 'Insufficient difficulty'];
        }
        
        return ['valid' => true];
    }
}
```

#### Kolka Reward System
```php
class KolkaRewardService {
    private $baseReward = 1.0;
    private $difficultyAdjustmentFactor = 0.1;
    
    public function calculateDifficulty($userId) {
        $userStats = $this->getUserMiningStats($userId);
        $globalStats = $this->getGlobalMiningStats();
        
        $userHashrate = $userStats['average_hashrate'];
        $globalHashrate = $globalStats['average_hashrate'];
        
        // Normalize difficulty based on user capability
        $difficultyMultiplier = min(
            max($userHashrate / $globalHashrate, 0.1), 
            10.0
        );
        
        return $this->baseDifficulty * $difficultyMultiplier;
    }
    
    public function calculateReward($userId, $jobDifficulty, $solutionTime) {
        $baseReward = $this->baseReward;
        $difficultyBonus = log($jobDifficulty + 1) * 0.1;
        $timeBonus = max(0, (300 - $solutionTime) / 300) * 0.2;
        
        return $baseReward + $difficultyBonus + $timeBonus;
    }
}
```

### Tamper-Evident Ledger Implementation

#### Block Structure
```php
class Block {
    public $index;
    public $timestamp;
    public $transactions;
    public $previousHash;
    public $merkleRoot;
    public $nonce;
    public $hash;
    
    public function __construct($index, $transactions, $previousHash) {
        $this->index = $index;
        $this->timestamp = time();
        $this->transactions = $transactions;
        $this->previousHash = $previousHash;
        $this->merkleRoot = $this->calculateMerkleRoot();
        $this->nonce = 0;
        $this->hash = $this->calculateHash();
    }
    
    private function calculateMerkleRoot() {
        $merkleTree = new MerkleTree($this->transactions);
        return $merkleTree->getRoot();
    }
    
    private function calculateHash() {
        $data = $this->index . 
                $this->timestamp . 
                $this->merkleRoot . 
                $this->previousHash . 
                $this->nonce;
        
        return hash('sha256', $data);
    }
}
```

#### Merkle Tree Implementation
```php
class MerkleTree {
    private $transactions;
    
    public function __construct($transactions) {
        $this->transactions = $transactions;
    }
    
    public function getRoot() {
        $hashes = array_map(function($tx) {
            return hash('sha256', json_encode($tx));
        }, $this->transactions);
        
        return $this->buildTree($hashes);
    }
    
    private function buildTree($hashes) {
        if (count($hashes) === 1) {
            return $hashes[0];
        }
        
        $nextLevel = [];
        for ($i = 0; $i < count($hashes); $i += 2) {
            $left = $hashes[$i];
            $right = isset($hashes[$i + 1]) ? $hashes[$i + 1] : $left;
            $nextLevel[] = hash('sha256', $left . $right);
        }
        
        return $this->buildTree($nextLevel);
    }
}
```

---

## Database Schema

### PostgreSQL Schema Design

#### Users Table
```sql
CREATE TABLE users (
    id SERIAL PRIMARY KEY,
    username VARCHAR(50) UNIQUE NOT NULL,
    email VARCHAR(255) UNIQUE NOT NULL,
    password_hash VARCHAR(255) NOT NULL,
    wallet_address VARCHAR(64) UNIQUE NOT NULL,
    balance DECIMAL(20, 8) DEFAULT 0.00000000,
    total_mined DECIMAL(20, 8) DEFAULT 0.00000000,
    hashrate_average BIGINT DEFAULT 0,
    difficulty_preference INTEGER DEFAULT 1,
    algorithm_preference VARCHAR(10) DEFAULT 'SHA1',
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    updated_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    last_mining_activity TIMESTAMP,
    is_active BOOLEAN DEFAULT true
);

CREATE INDEX idx_users_username ON users(username);
CREATE INDEX idx_users_wallet_address ON users(wallet_address);
CREATE INDEX idx_users_last_activity ON users(last_mining_activity);
```

#### Transactions Table
```sql
CREATE TABLE transactions (
    id SERIAL PRIMARY KEY,
    transaction_hash VARCHAR(64) UNIQUE NOT NULL,
    from_user_id INTEGER REFERENCES users(id),
    to_user_id INTEGER REFERENCES users(id),
    amount DECIMAL(20, 8) NOT NULL,
    transaction_type VARCHAR(20) NOT NULL, -- 'mining', 'transfer', 'reward'
    mining_job_id VARCHAR(64),
    block_id INTEGER,
    status VARCHAR(20) DEFAULT 'pending', -- 'pending', 'confirmed', 'failed'
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    confirmed_at TIMESTAMP,
    
    CONSTRAINT check_amount_positive CHECK (amount > 0),
    CONSTRAINT check_valid_type CHECK (transaction_type IN ('mining', 'transfer', 'reward', 'system'))
);

CREATE INDEX idx_transactions_hash ON transactions(transaction_hash);
CREATE INDEX idx_transactions_from_user ON transactions(from_user_id);
CREATE INDEX idx_transactions_to_user ON transactions(to_user_id);
CREATE INDEX idx_transactions_block ON transactions(block_id);
CREATE INDEX idx_transactions_type ON transactions(transaction_type);
CREATE INDEX idx_transactions_status ON transactions(status);
```

#### Blocks Table
```sql
CREATE TABLE blocks (
    id SERIAL PRIMARY KEY,
    block_index INTEGER UNIQUE NOT NULL,
    block_hash VARCHAR(64) UNIQUE NOT NULL,
    previous_hash VARCHAR(64) NOT NULL,
    merkle_root VARCHAR(64) NOT NULL,
    timestamp TIMESTAMP NOT NULL,
    transaction_count INTEGER NOT NULL,
    block_size INTEGER NOT NULL,
    difficulty DECIMAL(10, 4) NOT NULL,
    nonce BIGINT NOT NULL,
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    
    CONSTRAINT check_block_index_positive CHECK (block_index >= 0)
);

CREATE INDEX idx_blocks_index ON blocks(block_index);
CREATE INDEX idx_blocks_hash ON blocks(block_hash);
CREATE INDEX idx_blocks_previous_hash ON blocks(previous_hash);
CREATE INDEX idx_blocks_timestamp ON blocks(timestamp);
```

#### Mining Jobs Table
```sql
CREATE TABLE mining_jobs (
    id SERIAL PRIMARY KEY,
    job_id VARCHAR(64) UNIQUE NOT NULL,
    user_id INTEGER REFERENCES users(id),
    base_data TEXT NOT NULL,
    difficulty DECIMAL(10, 4) NOT NULL,
    algorithm VARCHAR(10) NOT NULL,
    status VARCHAR(20) DEFAULT 'active', -- 'active', 'completed', 'expired', 'cancelled'
    nonce BIGINT,
    solution_hash VARCHAR(64),
    solution_time INTEGER, -- Time taken to solve in seconds
    reward_amount DECIMAL(20, 8),
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    expires_at TIMESTAMP NOT NULL,
    completed_at TIMESTAMP,
    
    CONSTRAINT check_difficulty_positive CHECK (difficulty > 0),
    CONSTRAINT check_algorithm_valid CHECK (algorithm IN ('SHA1', 'SHA-256'))
);

CREATE INDEX idx_mining_jobs_job_id ON mining_jobs(job_id);
CREATE INDEX idx_mining_jobs_user_id ON mining_jobs(user_id);
CREATE INDEX idx_mining_jobs_status ON mining_jobs(status);
CREATE INDEX idx_mining_jobs_created_at ON mining_jobs(created_at);
CREATE INDEX idx_mining_jobs_expires_at ON mining_jobs(expires_at);
```

#### User Statistics Table
```sql
CREATE TABLE user_statistics (
    id SERIAL PRIMARY KEY,
    user_id INTEGER REFERENCES users(id),
    statistic_date DATE NOT NULL,
    total_hashes BIGINT DEFAULT 0,
    successful_jobs INTEGER DEFAULT 0,
    total_jobs INTEGER DEFAULT 0,
    average_solution_time DECIMAL(8, 2),
    coins_earned DECIMAL(20, 8) DEFAULT 0.00000000,
    peak_hashrate BIGINT DEFAULT 0,
    
    UNIQUE(user_id, statistic_date)
);

CREATE INDEX idx_user_stats_user_date ON user_statistics(user_id, statistic_date);
CREATE INDEX idx_user_stats_date ON user_statistics(statistic_date);
```

#### System Configuration Table
```sql
CREATE TABLE system_config (
    id SERIAL PRIMARY KEY,
    config_key VARCHAR(100) UNIQUE NOT NULL,
    config_value TEXT NOT NULL,
    config_type VARCHAR(20) DEFAULT 'string', -- 'string', 'integer', 'decimal', 'boolean', 'json'
    description TEXT,
    updated_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    updated_by INTEGER REFERENCES users(id)
);

-- Insert default configuration
INSERT INTO system_config (config_key, config_value, config_type, description) VALUES
('base_difficulty', '1.0', 'decimal', 'Base mining difficulty'),
('base_reward', '1.0', 'decimal', 'Base reward per solved job'),
('max_job_duration', '300', 'integer', 'Maximum job duration in seconds'),
('block_time_target', '600', 'integer', 'Target time between blocks in seconds'),
('transactions_per_block', '100', 'integer', 'Target transactions per block'),
('kolka_adjustment_factor', '0.1', 'decimal', 'Kolka difficulty adjustment factor');
```

#### Audit Log Table
```sql
CREATE TABLE audit_log (
    id SERIAL PRIMARY KEY,
    table_name VARCHAR(50) NOT NULL,
    record_id INTEGER NOT NULL,
    action VARCHAR(20) NOT NULL, -- 'INSERT', 'UPDATE', 'DELETE'
    old_values JSONB,
    new_values JSONB,
    changed_by INTEGER REFERENCES users(id),
    change_timestamp TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    change_reason TEXT
);

CREATE INDEX idx_audit_log_table_record ON audit_log(table_name, record_id);
CREATE INDEX idx_audit_log_timestamp ON audit_log(change_timestamp);
CREATE INDEX idx_audit_log_changed_by ON audit_log(changed_by);
```

---

## API Documentation

### Authentication

All API requests require authentication via JWT tokens or API keys.

#### Authentication Header
```
Authorization: Bearer <jwt_token>
```

### Core Endpoints

#### GET /api/v1/job
Request a new mining job.

**Parameters:**
- `user_id` (integer): User identifier
- `device_capability` (string): 'low' or 'high'
- `preferred_algorithm` (string): 'SHA1' or 'SHA-256'

**Response:**
```json
{
    "success": true,
    "data": {
        "job_id": "unique_job_identifier",
        "base_data": "data_to_hash",
        "difficulty": 2.5,
        "algorithm": "SHA-256",
        "expires_at": 1632150000
    }
}
```

#### POST /api/v1/submit
Submit a mining solution.

**Parameters:**
```json
{
    "job_id": "unique_job_identifier",
    "nonce": 12345678,
    "hash": "computed_hash_value",
    "computation_time": 45.2
}
```

**Response:**
```json
{
    "success": true,
    "data": {
        "valid": true,
        "reward": 1.25,
        "new_balance": 123.45,
        "transaction_id": "tx_hash"
    }
}
```

#### GET /api/v1/stats/user/{user_id}
Get user mining statistics.

**Response:**
```json
{
    "success": true,
    "data": {
        "balance": 123.45,
        "total_mined": 1000.50,
        "hashrate_average": 1500000,
        "jobs_completed": 150,
        "jobs_success_rate": 95.5,
        "last_mining_activity": "2025-09-21T05:16:53Z"
    }
}
```

#### GET /api/v1/ledger/blocks
Get blockchain information.

**Parameters:**
- `page` (integer): Page number (default: 1)
- `limit` (integer): Results per page (default: 10, max: 100)

**Response:**
```json
{
    "success": true,
    "data": {
        "blocks": [
            {
                "index": 1234,
                "hash": "block_hash",
                "previous_hash": "previous_block_hash",
                "merkle_root": "merkle_root_hash",
                "timestamp": "2025-09-21T05:16:53Z",
                "transaction_count": 25,
                "difficulty": 2.5
            }
        ],
        "pagination": {
            "current_page": 1,
            "total_pages": 50,
            "total_blocks": 1234
        }
    }
}
```

### WebSocket Events

#### Real-time Mining Updates
```javascript
// Client subscription
ws.send(JSON.stringify({
    type: 'subscribe',
    channel: 'mining_updates',
    user_id: 123
}));

// Server events
{
    "type": "job_completed",
    "data": {
        "user_id": 123,
        "reward": 1.25,
        "new_balance": 123.45,
        "hashrate": 1500000
    }
}

{
    "type": "difficulty_adjustment",
    "data": {
        "new_difficulty": 2.8,
        "reason": "hashrate_increase"
    }
}

{
    "type": "new_block",
    "data": {
        "block_index": 1235,
        "block_hash": "new_block_hash",
        "transaction_count": 30
    }
}
```

---

## Pseudocode

### Client-Side Mining Loop (Web Worker)

```javascript
// Main Mining Worker Pseudocode
FUNCTION MiningWorkerMain():
    INITIALIZE worker_state = {
        is_mining: false,
        current_job: null,
        hash_count: 0,
        start_time: null,
        algorithm: null
    }
    
    LISTEN FOR MESSAGE 'start_mining':
        job_data = received_message.job
        worker_state.current_job = job_data
        worker_state.is_mining = true
        worker_state.start_time = current_timestamp()
        worker_state.algorithm = job_data.algorithm
        
        CALL StartMiningLoop(job_data)
    
    LISTEN FOR MESSAGE 'stop_mining':
        worker_state.is_mining = false
        SEND MESSAGE 'mining_stopped' TO main_thread
    
    LISTEN FOR MESSAGE 'get_stats':
        stats = CalculateCurrentStats()
        SEND MESSAGE 'stats_update' WITH stats TO main_thread

FUNCTION StartMiningLoop(job):
    nonce = 0
    base_data = job.base_data
    target_difficulty = job.difficulty
    algorithm = job.algorithm
    
    WHILE worker_state.is_mining AND nonce < MAX_NONCE:
        // Generate test data
        test_data = base_data + nonce.toString()
        
        // Compute hash based on algorithm
        IF algorithm == 'SHA-256':
            hash = AWAIT ComputeSHA256(test_data)
        ELSE IF algorithm == 'SHA1':
            hash = AWAIT ComputeSHA1(test_data)
        
        // Check if hash meets difficulty
        IF ValidateHashDifficulty(hash, target_difficulty):
            solution = {
                nonce: nonce,
                hash: hash,
                computation_time: current_timestamp() - worker_state.start_time,
                hash_count: worker_state.hash_count
            }
            
            SEND MESSAGE 'solution_found' WITH solution TO main_thread
            BREAK
        
        nonce++
        worker_state.hash_count++
        
        // Report progress every 1000 hashes
        IF worker_state.hash_count % 1000 == 0:
            progress = {
                hash_count: worker_state.hash_count,
                current_hashrate: CalculateHashrate(),
                elapsed_time: current_timestamp() - worker_state.start_time
            }
            SEND MESSAGE 'progress_update' WITH progress TO main_thread
        
        // Yield control periodically
        IF worker_state.hash_count % 10000 == 0:
            AWAIT sleep(1) // Brief pause to prevent blocking
    
    IF nonce >= MAX_NONCE:
        SEND MESSAGE 'job_exhausted' TO main_thread

FUNCTION ComputeSHA256(data):
    encoder = new TextEncoder()
    data_buffer = encoder.encode(data)
    hash_buffer = AWAIT crypto.subtle.digest('SHA-256', data_buffer)
    RETURN BufferToHex(hash_buffer)

FUNCTION ComputeSHA1(data):
    encoder = new TextEncoder()
    data_buffer = encoder.encode(data)
    hash_buffer = AWAIT crypto.subtle.digest('SHA-1', data_buffer)
    RETURN BufferToHex(hash_buffer)

FUNCTION ValidateHashDifficulty(hash, difficulty):
    // Count leading zeros in hash
    leading_zeros = 0
    FOR each character in hash:
        IF character == '0':
            leading_zeros++
        ELSE:
            BREAK
    
    RETURN leading_zeros >= difficulty

FUNCTION CalculateHashrate():
    current_time = current_timestamp()
    elapsed = current_time - worker_state.start_time
    IF elapsed > 0:
        RETURN worker_state.hash_count / elapsed
    ELSE:
        RETURN 0
```

### Server-Side Job Assignment Logic

```php
// Server Job Assignment Pseudocode
FUNCTION AssignMiningJob(user_id, device_capability):
    // Get user's current statistics
    user_stats = GetUserMiningStats(user_id)
    global_stats = GetGlobalMiningStats()
    
    // Calculate personalized difficulty using Kolka system
    kolka_difficulty = CalculateKolkaDifficulty(user_stats, global_stats)
    
    // Adjust for device capability
    IF device_capability == 'low':
        final_difficulty = kolka_difficulty * 0.7
        preferred_algorithm = 'SHA1'
    ELSE:
        final_difficulty = kolka_difficulty
        preferred_algorithm = 'SHA-256'
    
    // Generate unique job data
    job_data = {
        job_id: GenerateUniqueJobId(),
        user_id: user_id,
        base_data: GenerateBaseData(),
        difficulty: final_difficulty,
        algorithm: preferred_algorithm,
        created_at: current_timestamp(),
        expires_at: current_timestamp() + JOB_EXPIRY_TIME
    }
    
    // Store job in database
    StoreJobInDatabase(job_data)
    
    // Return job to client
    RETURN job_data

FUNCTION CalculateKolkaDifficulty(user_stats, global_stats):
    base_difficulty = GetSystemConfig('base_difficulty')
    adjustment_factor = GetSystemConfig('kolka_adjustment_factor')
    
    // Calculate user's relative performance
    user_hashrate = user_stats.average_hashrate
    global_hashrate = global_stats.average_hashrate
    
    IF global_hashrate > 0:
        performance_ratio = user_hashrate / global_hashrate
    ELSE:
        performance_ratio = 1.0
    
    // Normalize ratio to prevent extreme values
    normalized_ratio = CLAMP(performance_ratio, 0.1, 10.0)
    
    // Calculate adjusted difficulty
    kolka_difficulty = base_difficulty * (1 + (normalized_ratio - 1) * adjustment_factor)
    
    RETURN MAX(kolka_difficulty, 0.1) // Minimum difficulty

FUNCTION ValidateJobSubmission(job_id, nonce, submitted_hash, computation_time):
    // Retrieve job from database
    job = GetJobFromDatabase(job_id)
    
    IF job == null:
        RETURN {valid: false, reason: 'Job not found'}
    
    IF job.expires_at < current_timestamp():
        RETURN {valid: false, reason: 'Job expired'}
    
    IF job.status != 'active':
        RETURN {valid: false, reason: 'Job no longer active'}
    
    // Recreate expected hash
    test_data = job.base_data + nonce.toString()
    
    IF job.algorithm == 'SHA-256':
        expected_hash = ComputeSHA256(test_data)
    ELSE IF job.algorithm == 'SHA1':
        expected_hash = ComputeSHA1(test_data)
    
    // Validate hash matches
    IF expected_hash != submitted_hash:
        RETURN {valid: false, reason: 'Hash mismatch'}
    
    // Validate difficulty requirement
    IF NOT ValidateHashDifficulty(submitted_hash, job.difficulty):
        RETURN {valid: false, reason: 'Insufficient difficulty'}
    
    // Calculate reward
    reward = CalculateReward(job, computation_time)
    
    // Update job status
    UpdateJobStatus(job_id, 'completed', nonce, submitted_hash, reward)
    
    // Add transaction to ledger
    transaction_id = AddTransaction(job.user_id, reward, 'mining', job_id)
    
    // Update user balance
    UpdateUserBalance(job.user_id, reward)
    
    RETURN {
        valid: true,
        reward: reward,
        transaction_id: transaction_id,
        new_balance: GetUserBalance(job.user_id)
    }

FUNCTION CalculateReward(job, computation_time):
    base_reward = GetSystemConfig('base_reward')
    
    // Difficulty bonus (logarithmic scaling)
    difficulty_bonus = LOG(job.difficulty + 1) * 0.1
    
    // Time bonus (faster solutions get slight bonus)
    max_time = GetSystemConfig('max_job_duration')
    time_bonus = MAX(0, (max_time - computation_time) / max_time) * 0.2
    
    // User loyalty bonus (based on total mined)
    user_stats = GetUserMiningStats(job.user_id)
    loyalty_bonus = MIN(LOG(user_stats.total_mined + 1) * 0.05, 0.5)
    
    total_reward = base_reward + difficulty_bonus + time_bonus + loyalty_bonus
    
    RETURN total_reward
```

### Tamper-Evident Ledger Logic

```php
// Blockchain Ledger Pseudocode
FUNCTION CreateNewBlock(pending_transactions):
    latest_block = GetLatestBlock()
    
    IF latest_block == null:
        // Genesis block
        previous_hash = '0000000000000000000000000000000000000000000000000000000000000000'
        block_index = 0
    ELSE:
        previous_hash = latest_block.hash
        block_index = latest_block.index + 1
    
    // Create Merkle tree for transactions
    merkle_root = CalculateMerkleRoot(pending_transactions)
    
    // Create block
    new_block = {
        index: block_index,
        timestamp: current_timestamp(),
        transactions: pending_transactions,
        previous_hash: previous_hash,
        merkle_root: merkle_root,
        nonce: 0
    }
    
    // Calculate block hash
    new_block.hash = CalculateBlockHash(new_block)
    
    // Store block in database
    StoreBlockInDatabase(new_block)
    
    // Update transaction statuses
    FOR each transaction in pending_transactions:
        UpdateTransactionStatus(transaction.id, 'confirmed', block_index)
    
    // Generate database digest for immutable storage
    GenerateDatabaseDigest(new_block)
    
    RETURN new_block

FUNCTION CalculateMerkleRoot(transactions):
    IF transactions.length == 0:
        RETURN CalculateHash('empty_block')
    
    // Create leaf hashes
    leaf_hashes = []
    FOR each transaction in transactions:
        transaction_data = SerializeTransaction(transaction)
        leaf_hashes.append(CalculateHash(transaction_data))
    
    // Build Merkle tree bottom-up
    current_level = leaf_hashes
    
    WHILE current_level.length > 1:
        next_level = []
        
        FOR i = 0 to current_level.length step 2:
            left = current_level[i]
            
            IF i + 1 < current_level.length:
                right = current_level[i + 1]
            ELSE:
                right = left // Duplicate last hash if odd number
            
            combined_hash = CalculateHash(left + right)
            next_level.append(combined_hash)
        
        current_level = next_level
    
    RETURN current_level[0]

FUNCTION CalculateBlockHash(block):
    block_data = block.index.toString() +
                block.timestamp.toString() +
                block.merkle_root +
                block.previous_hash +
                block.nonce.toString()
    
    RETURN CalculateHash(block_data)

FUNCTION VerifyBlockchain():
    blocks = GetAllBlocksOrdered()
    
    FOR i = 1 to blocks.length - 1:
        current_block = blocks[i]
        previous_block = blocks[i - 1]
        
        // Verify previous hash link
        IF current_block.previous_hash != previous_block.hash:
            RETURN {valid: false, error: 'Hash chain broken at block ' + i}
        
        // Verify block hash
        calculated_hash = CalculateBlockHash(current_block)
        IF calculated_hash != current_block.hash:
            RETURN {valid: false, error: 'Invalid block hash at block ' + i}
        
        // Verify Merkle root
        block_transactions = GetBlockTransactions(current_block.index)
        calculated_merkle = CalculateMerkleRoot(block_transactions)
        IF calculated_merkle != current_block.merkle_root:
            RETURN {valid: false, error: 'Invalid Merkle root at block ' + i}
    
    RETURN {valid: true}

FUNCTION GenerateDatabaseDigest(new_block):
    // Create comprehensive database state hash
    all_users = GetAllUsers()
    all_transactions = GetAllTransactions()
    all_blocks = GetAllBlocks()
    
    digest_data = {
        timestamp: current_timestamp(),
        last_block_hash: new_block.hash,
        total_users: all_users.length,
        total_transactions: all_transactions.length,
        total_blocks: all_blocks.length,
        user_checksums: CalculateUserChecksums(all_users),
        transaction_checksums: CalculateTransactionChecksums(all_transactions),
        blockchain_integrity: VerifyBlockchain()
    }
    
    digest_hash = CalculateHash(JSON.stringify(digest_data))
    
    // Store in immutable storage
    StoreInImmutableStorage({
        digest_hash: digest_hash,
        data: digest_data,
        created_at: current_timestamp()
    })
    
    RETURN digest_hash
```

---

## Development Phases

### Phase 1: Minimum Viable Product (MVP)

**Duration**: 4-6 weeks  
**Goal**: Basic functional mining system

#### Week 1-2: Backend Foundation
- [x] Database schema implementation
- [x] Basic PHP API framework setup
- [x] User authentication system
- [x] Mining job generation and validation
- [x] Simple reward calculation

#### Week 3-4: Frontend Development
- [x] HTML5 miner interface
- [x] Web Worker implementation
- [x] Basic cryptographic functions
- [x] API communication layer
- [x] Real-time statistics display

#### Week 5-6: Integration and Testing
- [x] End-to-end testing
- [x] Performance optimization
- [x] Bug fixes and stability improvements
- [x] Basic documentation

**MVP Deliverables:**
- Functional mining interface
- Job assignment and validation
- Basic user accounts and balances
- SHA1 and SHA-256 support
- Simple statistics tracking

### Phase 2: UI/UX and System Enhancements

**Duration**: 6-8 weeks  
**Goal**: Professional interface and advanced features

#### Week 1-2: Kolka Reward System
- [x] Dynamic difficulty adjustment algorithm
- [x] User performance tracking
- [x] Reward calculation improvements
- [x] Fair distribution mechanisms

#### Week 3-4: Tamper-Evident Ledger
- [x] Merkle tree implementation
- [x] Block creation and chaining
- [x] Blockchain verification
- [x] Immutable storage integration

#### Week 5-6: Enhanced UI/UX
- [x] Modern responsive design
- [x] Real-time dashboard
- [x] Mining statistics and graphs
- [x] User profile management
- [x] Transaction history

#### Week 7-8: Advanced Features
- [x] WebSocket real-time updates
- [x] Mining pools support
- [x] Advanced analytics
- [x] Security enhancements

**Phase 2 Deliverables:**
- Complete Kolka reward system
- Fully functional blockchain ledger
- Professional user interface
- Real-time monitoring and updates
- Comprehensive testing suite

### Phase 3: Community and Ecosystem

**Duration**: 8-10 weeks  
**Goal**: Community features and external integrations

#### Week 1-3: Blockchain Integration
- [ ] Cross-chain wrapping functionality
- [ ] External blockchain connectors
- [ ] Multi-signature wallet support
- [ ] Exchange integration preparation

#### Week 4-6: Developer Ecosystem
- [ ] Public API documentation
- [ ] Third-party client SDK
- [ ] Wallet development kit
- [ ] Mining pool software

#### Week 7-8: Community Features
- [ ] User forums and discussions
- [ ] Achievement system
- [ ] Leaderboards and competitions
- [ ] Social mining features

#### Week 9-10: Scaling and Optimization
- [ ] Performance improvements
- [ ] Load balancing
- [ ] Caching optimization
- [ ] Mobile application

**Phase 3 Deliverables:**
- Cross-blockchain compatibility
- Third-party development ecosystem
- Community engagement features
- Production-ready scaling
- Mobile applications

---

## Security Considerations

### Client-Side Security

#### Input Validation
```javascript
function validateJobData(job) {
    const requiredFields = ['job_id', 'base_data', 'difficulty', 'algorithm', 'expires_at'];
    
    for (const field of requiredFields) {
        if (!job.hasOwnProperty(field)) {
            throw new Error(`Missing required field: ${field}`);
        }
    }
    
    if (job.difficulty < 0.1 || job.difficulty > 100) {
        throw new Error('Invalid difficulty range');
    }
    
    if (!['SHA1', 'SHA-256'].includes(job.algorithm)) {
        throw new Error('Unsupported algorithm');
    }
    
    return true;
}
```

#### Rate Limiting
```javascript
class RateLimiter {
    constructor(maxRequests = 10, timeWindow = 60000) {
        this.maxRequests = maxRequests;
        this.timeWindow = timeWindow;
        this.requests = [];
    }
    
    canMakeRequest() {
        const now = Date.now();
        this.requests = this.requests.filter(time => now - time < this.timeWindow);
        
        if (this.requests.length >= this.maxRequests) {
            return false;
        }
        
        this.requests.push(now);
        return true;
    }
}
```

### Server-Side Security

#### Input Sanitization
```php
class SecurityValidator {
    public static function validateJobSubmission($data) {
        // Validate job_id format
        if (!preg_match('/^[a-zA-Z0-9_-]{1,64}$/', $data['job_id'])) {
            throw new InvalidArgumentException('Invalid job_id format');
        }
        
        // Validate nonce range
        if (!is_numeric($data['nonce']) || $data['nonce'] < 0 || $data['nonce'] > PHP_INT_MAX) {
            throw new InvalidArgumentException('Invalid nonce value');
        }
        
        // Validate hash format
        if (!preg_match('/^[a-f0-9]{40,64}$/', $data['hash'])) {
            throw new InvalidArgumentException('Invalid hash format');
        }
        
        return true;
    }
    
    public static function sanitizeUserInput($input) {
        return htmlspecialchars(trim($input), ENT_QUOTES, 'UTF-8');
    }
}
```

#### SQL Injection Prevention
```php
class DatabaseManager {
    private $pdo;
    
    public function getUser($userId) {
        $stmt = $this->pdo->prepare('SELECT * FROM users WHERE id = ? AND is_active = true');
        $stmt->execute([$userId]);
        return $stmt->fetch(PDO::FETCH_ASSOC);
    }
    
    public function createTransaction($fromUserId, $toUserId, $amount, $type) {
        $stmt = $this->pdo->prepare('
            INSERT INTO transactions (from_user_id, to_user_id, amount, transaction_type, transaction_hash)
            VALUES (?, ?, ?, ?, ?)
        ');
        
        $transactionHash = hash('sha256', $fromUserId . $toUserId . $amount . $type . time());
        $stmt->execute([$fromUserId, $toUserId, $amount, $type, $transactionHash]);
        
        return $this->pdo->lastInsertId();
    }
}
```

### Cryptographic Security

#### Hash Validation
```php
class CryptoValidator {
    public static function validateHash($data, $nonce, $hash, $algorithm) {
        $testData = $data . $nonce;
        
        switch ($algorithm) {
            case 'SHA-256':
                $expectedHash = hash('sha256', $testData);
                break;
            case 'SHA1':
                $expectedHash = sha1($testData);
                break;
            default:
                throw new InvalidArgumentException('Unsupported algorithm');
        }
        
        return hash_equals($expectedHash, $hash);
    }
    
    public static function validateDifficulty($hash, $requiredDifficulty) {
        $leadingZeros = 0;
        $hashLength = strlen($hash);
        
        for ($i = 0; $i < $hashLength; $i++) {
            if ($hash[$i] === '0') {
                $leadingZeros++;
            } else {
                break;
            }
        }
        
        return $leadingZeros >= $requiredDifficulty;
    }
}
```

### Network Security

#### HTTPS Enforcement
```php
// Force HTTPS in production
if (!isset($_SERVER['HTTPS']) || $_SERVER['HTTPS'] !== 'on') {
    if (getenv('ENVIRONMENT') === 'production') {
        $redirectURL = 'https://' . $_SERVER['HTTP_HOST'] . $_SERVER['REQUEST_URI'];
        header("Location: $redirectURL");
        exit();
    }
}
```

#### CORS Configuration
```php
// Set appropriate CORS headers
header('Access-Control-Allow-Origin: https://planet9coin.com');
header('Access-Control-Allow-Methods: GET, POST, OPTIONS');
header('Access-Control-Allow-Headers: Content-Type, Authorization');
header('Access-Control-Max-Age: 86400');

if ($_SERVER['REQUEST_METHOD'] === 'OPTIONS') {
    exit(0);
}
```

---

## Deployment Guidelines

### System Requirements

#### Minimum Server Requirements
- **CPU**: 2 cores, 2.4 GHz
- **RAM**: 4 GB
- **Storage**: 50 GB SSD
- **Network**: 100 Mbps
- **OS**: Ubuntu 20.04 LTS or newer

#### Recommended Server Requirements
- **CPU**: 4 cores, 3.0 GHz
- **RAM**: 8 GB
- **Storage**: 200 GB SSD
- **Network**: 1 Gbps
- **OS**: Ubuntu 22.04 LTS

#### Software Stack
- **Web Server**: Nginx 1.20+
- **PHP**: PHP 8.2+ with FPM
- **Database**: PostgreSQL 14+
- **Cache**: Redis 6.0+
- **SSL**: Let's Encrypt or commercial certificate

### Installation Steps

#### 1. System Preparation
```bash
# Update system
sudo apt update && sudo apt upgrade -y

# Install required packages
sudo apt install -y nginx php8.2-fpm php8.2-pgsql php8.2-redis \
    php8.2-curl php8.2-json php8.2-mbstring php8.2-xml \
    postgresql-14 redis-server git composer

# Enable services
sudo systemctl enable nginx php8.2-fpm postgresql redis-server
sudo systemctl start nginx php8.2-fpm postgresql redis-server
```

#### 2. Database Setup
```sql
-- Create database and user
CREATE DATABASE planet9coin;
CREATE USER planet9_user WITH PASSWORD 'secure_password_here';
GRANT ALL PRIVILEGES ON DATABASE planet9coin TO planet9_user;

-- Connect to database and run migrations
\c planet9coin;
-- Execute schema from database section above
```

#### 3. Application Deployment
```bash
# Clone repository
git clone https://github.com/yourusername/planet9coin.git /var/www/planet9coin
cd /var/www/planet9coin

# Install dependencies
composer install --no-dev --optimize-autoloader

# Set permissions
sudo chown -R www-data:www-data /var/www/planet9coin
sudo chmod -R 755 /var/www/planet9coin
sudo chmod -R 775 /var/www/planet9coin/storage
sudo chmod -R 775 /var/www/planet9coin/cache
```

#### 4. Nginx Configuration
```nginx
server {
    listen 80;
    server_name planet9coin.com www.planet9coin.com;
    return 301 https://$server_name$request_uri;
}

server {
    listen 443 ssl http2;
    server_name planet9coin.com www.planet9coin.com;
    
    root /var/www/planet9coin/public;
    index index.php index.html;
    
    ssl_certificate /etc/letsencrypt/live/planet9coin.com/fullchain.pem;
    ssl_certificate_key /etc/letsencrypt/live/planet9coin.com/privkey.pem;
    
    # SSL settings
    ssl_protocols TLSv1.2 TLSv1.3;
    ssl_ciphers ECDHE-RSA-AES256-GCM-SHA512:DHE-RSA-AES256-GCM-SHA512;
    ssl_prefer_server_ciphers off;
    
    # Security headers
    add_header X-Frame-Options "SAMEORIGIN" always;
    add_header X-XSS-Protection "1; mode=block" always;
    add_header X-Content-Type-Options "nosniff" always;
    add_header Referrer-Policy "no-referrer-when-downgrade" always;
    add_header Content-Security-Policy "default-src 'self' http: https: data: blob: 'unsafe-inline'" always;
    
    location / {
        try_files $uri $uri/ /index.php?$query_string;
    }
    
    location ~ \.php$ {
        fastcgi_pass unix:/var/run/php/php8.2-fpm.sock;
        fastcgi_index index.php;
        fastcgi_param SCRIPT_FILENAME $realpath_root$fastcgi_script_name;
        include fastcgi_params;
    }
    
    location ~ /\.(?!well-known).* {
        deny all;
    }
    
    # API rate limiting
    location /api/ {
        limit_req zone=api burst=20 nodelay;
        try_files $uri $uri/ /index.php?$query_string;
    }
}

# Rate limiting configuration
http {
    limit_req_zone $binary_remote_addr zone=api:10m rate=10r/s;
}
```

#### 5. SSL Certificate Setup
```bash
# Install Certbot
sudo apt install -y certbot python3-certbot-nginx

# Obtain SSL certificate
sudo certbot --nginx -d planet9coin.com -d www.planet9coin.com

# Setup automatic renewal
sudo crontab -e
# Add: 0 12 * * * /usr/bin/certbot renew --quiet
```

### Monitoring and Maintenance

#### Health Check Script
```bash
#!/bin/bash
# health_check.sh

# Check services
services=("nginx" "php8.2-fpm" "postgresql" "redis-server")
for service in "${services[@]}"; do
    if ! systemctl is-active --quiet $service; then
        echo "ERROR: $service is not running"
        systemctl restart $service
    fi
done

# Check database connectivity
php -r "
    try {
        \$pdo = new PDO('pgsql:host=localhost;dbname=planet9coin', 'planet9_user', 'password');
        echo 'Database: OK\n';
    } catch (Exception \$e) {
        echo 'Database: ERROR - ' . \$e->getMessage() . '\n';
    }
"

# Check disk space
usage=$(df / | awk 'NR==2 {print $5}' | sed 's/%//')
if [ $usage -gt 80 ]; then
    echo "WARNING: Disk usage is ${usage}%"
fi

# Check memory usage
mem_usage=$(free | awk 'NR==2{printf "%.2f%%", $3*100/$2}')
echo "Memory usage: $mem_usage"
```

#### Backup Script
```bash
#!/bin/bash
# backup.sh

BACKUP_DIR="/backups/planet9coin"
DATE=$(date +%Y%m%d_%H%M%S)

# Create backup directory
mkdir -p $BACKUP_DIR

# Database backup
pg_dump -U planet9_user -h localhost planet9coin | gzip > $BACKUP_DIR/database_$DATE.sql.gz

# Application files backup
tar -czf $BACKUP_DIR/files_$DATE.tar.gz /var/www/planet9coin

# Clean old backups (keep 30 days)
find $BACKUP_DIR -name "*.gz" -mtime +30 -delete

echo "Backup completed: $DATE"
```

#### Performance Monitoring
```bash
# Setup system monitoring
sudo apt install -y htop iotop nethogs

# Monitor key metrics
echo "=== System Status ===" > /var/log/planet9coin_status.log
echo "Date: $(date)" >> /var/log/planet9coin_status.log
echo "Load: $(uptime)" >> /var/log/planet9coin_status.log
echo "Memory: $(free -h)" >> /var/log/planet9coin_status.log
echo "Disk: $(df -h /)" >> /var/log/planet9coin_status.log
echo "Active connections: $(netstat -an | grep :443 | wc -l)" >> /var/log/planet9coin_status.log
```

---

## Conclusion

Project Planet 9 Coin represents a comprehensive educational cryptocurrency system that balances simplicity with security. The centralized architecture enables rapid development and easy maintenance while the tamper-evident ledger provides transparency and trust.

### Key Strengths
- **Educational Value**: Clear, understandable implementation
- **Accessibility**: HTML5-based mining works on any modern browser
- **Fairness**: Kolka reward system ensures equitable distribution
- **Security**: Multiple layers of cryptographic protection
- **Scalability**: Designed for growth and community development

### Future Enhancements
- Cross-blockchain integration
- Mobile applications
- Advanced analytics and reporting
- Community governance features
- Educational content and tutorials

This specification provides a complete blueprint for implementing Planet 9 Coin, from initial development through production deployment. The modular design allows for incremental development and easy customization based on community needs.

---

**End of Technical Specification**

*This document serves as the authoritative technical reference for Project Planet 9 Coin. All implementation should follow these specifications to ensure consistency, security, and reliability.*
