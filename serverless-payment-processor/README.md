# ⚡ Serverless Payment Processor

**Milestone:** I just completed and deployed my first serverless function using AWS Lambda! 🚀

## Overview
This project simulates a backend fintech service that processes incoming payment alerts. By utilizing AWS Lambda, this function runs entirely serverless, executing only when triggered and requiring zero EC2 instance management.

## Technical Details
* **Runtime:** Python 3.12
* **Architecture:** AWS Lambda (Serverless)
* **Key Features:**
  * Extracts and validates JSON payload data (`amount`, `sender`, `recipient`).
  * Utilizes Environment Variables (`CURRENCY`) for flexible configuration.
  * Includes automated error handling for invalid payment amounts (HTTP 400).
  * Native logging integration with Amazon CloudWatch.

## The Code
The core execution logic is contained within `lambda_function.py`
