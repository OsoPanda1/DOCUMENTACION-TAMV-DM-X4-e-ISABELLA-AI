# Implementation Plan: TAMVAI API

## Overview

This implementation plan breaks down the TAMVAI API development into incremental steps, building from core infrastructure through to advanced features. Each task builds on previous work, with testing integrated throughout to catch errors early. The implementation follows a microservices architecture using TypeScript/Node.js with Express, PostgreSQL, and Redis.

## Tasks

- [x] 1. Set up project structure and core dependencies
  - Create `packages/tamvai-api` directory structure
  - Initialize package.json with dependencies (express, pg, redis, bcrypt, jsonwebtoken, fast-check, jest)
  - Set up TypeScript configuration
  - Create database schema SQL files
  - Set up environment configuration (.env.example)
  - _Requirements: All (foundational)_

- [ ] 2. Implement database layer and migrations
  - [x] 2.1 Create PostgreSQL schema for api_keys, users, permissions, models, audit_logs, token_usage tables
    - Write migration scripts for all tables with proper indexes
    - _Requirements: 2.7, 9.5, 9.6, 10.5_
  
  - [x] 2.2 Implement database connection pool and health checks
    - Create database client wrapper with connection pooling
    - Implement health check queries
    - _Requirements: 14.2_
  
  - [ ]* 2.3 Write unit tests for database layer
    - Test connection handling, query execution, error handling
    - _Requirements: 2.7, 9.5, 9.6_

- [ ] 3. Implement Redis integration for caching and rate limiting
  - [x] 3.1 Create Redis client wrapper with connection handling
    - Implement Redis connection with error handling and reconnection logic
    - _Requirements: 3.6_
  
  - [x] 3.2 Implement rate limiting data structures in Redis
    - Create functions for setting/getting rate limit counters with TTL
    - Implement per-minute and per-day counters
    - _Requirements: 3.6, 3.7_
  
  - [ ]* 3.3 Write property test for rate limit TTL management
    - **Property 6: Rate Limit TTL Management**
    - **Validates: Requirements 3.7**

- [ ] 4. Implement Authentication Service
  - [x] 4.1 Create API key generation and hashing functions
    - Implement cryptographically secure key generation (format: tamv-xxxxxxxxxxxxxxxxxxxx)
    - Implement bcrypt hashing with salt rounds >= 12
    - _Requirements: 2.4, 2.5, 2.7_
  
  - [x] 4.2 Implement API key validation and user context retrieval
    - Create validateApiKey function that checks hash and retrieves permissions
    - Implement revocation checking
    - _Requirements: 2.1, 2.2, 2.3, 2.6_
  
  - [ ]* 4.3 Write property test for API key format and security
    - **Property 4: API Key Format and Security**
    - **Validates: Requirements 2.4, 2.5, 2.7**
  
  - [ ]* 4.4 Write property test for authentication flow
    - **Property 3: API Key Authentication Flow**
    - **Validates: Requirements 2.1, 2.2, 2.3, 2.6**
  
  - [ ]* 4.5 Write unit tests for edge cases
    - Test expired keys, revoked keys, malformed keys
    - _Requirements: 2.1, 2.2, 2.6_

- [ ] 5. Implement Rate Limiter Service
  - [ ] 5.1 Create rate limiting logic with Redis backend
    - Implement checkRateLimit function with per-minute and per-day limits
    - Implement tier-based limits (free, basic, premium, enterprise)
    - Calculate Retry-After header values
    - _Requirements: 3.1, 3.2, 3.3, 3.4, 3.5_
  
  - [ ] 5.2 Implement token usage tracking and warnings
    - Create incrementUsage function
    - Implement warning logic for approaching limits (>90%)
    - _Requirements: 12.6_
  
  - [ ]* 5.3 Write property test for rate limiting enforcement
    - **Property 5: Rate Limiting Enforcement**
    - **Validates: Requirements 3.1, 3.2, 3.3, 3.4, 3.5**
  
  - [ ]* 5.4 Write property test for token limit warnings
    - **Property 29: Token Limit Warnings**
    - **Validates: Requirements 12.6**

- [ ] 6. Checkpoint - Core infrastructure validation
  - Ensure all tests pass, verify database and Redis connectivity, ask the user if questions arise.

- [ ] 7. Implement Model Registry Service
  - [ ] 7.1 Create model configuration loading and validation
    - Implement loadModels function that reads from config files
    - Validate model configurations (required fields, valid backend types)
    - _Requirements: 15.1, 15.4_
  
  - [ ] 7.2 Implement model CRUD operations
    - Create registerModel, getModel, listModels, updateModelStatus functions
    - Store models in PostgreSQL with health status tracking
    - _Requirements: 4.1, 4.7, 8.1, 8.5_
  
  - [ ] 7.3 Implement dynamic configuration reload
    - Create hot reload mechanism for model configuration changes
    - Implement configuration validation and rollback on errors
    - _Requirements: 15.2, 15.3, 15.7_
  
  - [ ]* 7.4 Write property test for model registry completeness
    - **Pro