# Project Overview

## ORELLI Footwear Factory Automation

ORELLI Footwear Factory Automation is an end-to-end operational automation system for footwear manufacturing.

The project combines PostgreSQL, n8n, Docker, Google Sheets, Telegram, PDF processing, and LLM-based reasoning to provide a structured backend for factory operations.

## Problem

Factory operations can become fragmented across:

- spreadsheets
- messaging
- paper purchase orders
- manual production updates
- worker wage calculations
- material records
- supplier information
- QC records
- dispatch records

The project centralizes these processes around PostgreSQL while retaining familiar interfaces such as Telegram and Google Sheets.

## Major Functional Areas

The system covers:

- Purchase-order intake
- SKU mapping
- BOM
- Inventory
- Procurement
- Suppliers
- Production
- Scheduling
- Worker production
- Wages
- Worker advances
- Payments
- Factory expenses
- Quality control
- Dispatch
- Grindery operations
- Factory reporting
- AI-assisted analysis

## AI Factory Copilot

A Telegram-based AI Copilot provides natural-language access to factory data.

The AI can:

- inspect operational data
- analyze factory activity
- compare information across domains
- recommend actions
- prepare database mutations
- support natural-language undo

Business mutations are confirmation-gated.

## Source of Truth

PostgreSQL is the authoritative operational database.

n8n provides workflow orchestration.

Google Sheets provides synchronized operational visibility where required.

Telegram provides the main conversational interface for the AI Copilot.

## Current Technical Scale

The PostgreSQL implementation contains:

- 107 application tables
- 32 views
- 50 PostgreSQL functions
- 62 sequences

The repository contains sanitized exports of 13 major n8n workflows.

## Repository Goal

This repository documents the engineering implementation while excluding production credentials and sensitive factory data.
