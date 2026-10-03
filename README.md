# Nullstice Discord Client

A Java Discord client for the Nullstice challenge platform.

> This repository contains the Discord client only.
> The Nullstice backend is a separate project and is responsible for
> authentication, accounts, challenges, attempts, answer validation,
> progress/points, leaderboard data, and persistence.

## Overview

The Discord client provides a Discord interface to the Nullstice API.

Users will be able to interact with Nullstice through Discord instead of
directly calling the REST API.

### Planned features

- `/profile`
- `/challenges`
- View challenge details
- Start/resume challenge attempts
- Submit challenge answers
- `/progress`
- `/leaderboard`

## Architecture

Discord User
    |
    v
Java Discord Client
    |
    | REST API
    v
Nullstice Backend
    |
    +-- Accounts
    +-- Authentication
    +-- Challenges
    +-- Attempts
    +-- Answer validation
    +-- Progress / Points
    +-- Leaderboard
    +-- Database

The Discord client is intentionally thin.

It should send requests to the backend API and display the returned data.
Business logic belongs to the backend.

## Technology

- Java
- JDA
- Java HTTP Client
- JSON serialization/deserialization
- REST API
- Gradle

## Nullstice API

The client is built against the Nullstice API contract.

Current API functionality includes:

### Identity

- Discord OAuth2 authentication
- User-bound Nullstice API tokens
- `GET /api/v1/me`

### Challenges

- `GET /api/v1/challenges`
- `GET /api/v1/challenges/{challengeId}`

### Attempts

- `GET /api/v1/challenges/{challengeId}/attempts/current`
- `POST /api/v1/challenges/{challengeId}/attempts`
- `GET /api/v1/attempts/{attemptId}`

### Submissions

- `POST /api/v1/attempts/{attemptId}/submissions`

### Progress

- `GET /api/v1/progress`

### Leaderboard

- `GET /api/v1/leaderboard`

## Progress / Points

Nullstice uses a **Progress / Points** system.

There is no XP system in this client.

The backend determines whether an answer is correct and awards points.
The Discord client only displays the result returned by the API.

The client must never calculate or modify a user's points itself.

## Challenge flow

A typical challenge interaction will work like this:

1. User opens `/challenges`.
2. The client requests the challenge catalogue from the backend.
3. User selects an unlocked challenge.
4. The client requests the challenge details.
5. User starts or resumes an attempt.
6. The backend provides the generated input.
7. User submits their answer through Discord.
8. The client sends the answer to the backend.
9. The backend grades the answer.
10. The client displays the result and updated points.

Expected answers remain private and are never stored by the Discord client.

## Authentication

The API supports:

- Discord OAuth2 browser authentication
- User-bound Nullstice API tokens

A Nullstice API token is not a Discord access token.

Tokens are sensitive credentials and must never be committed to Git.

## Account Linking

The current API contract does not define a Discord-bot-specific
account-linking endpoint.

Because of this, the client will not invent its own account-linking system.

The backend team needs to define the Discord <-> Nullstice account
linking flow before this part is implemented.

Once the backend contract exists, the client can integrate with it.

## Privacy

The client follows a minimal-data approach.

The client should not store:

- Discord passwords
- Nullstice passwords
- Discord OAuth access tokens
- Discord OAuth refresh tokens
- Nullstice API tokens in source code
- Expected challenge answers
- Discord message history
- IP addresses
- Device information
- Unrelated Discord account information

User-submitted answers should only exist long enough to send them to
the backend and receive the grading result.

## Configuration

Create a `.env` file:

```env
DISCORD_BOT_TOKEN=your_discord_bot_token
NULLSTICE_API_BASE_URL=http://localhost:8080

Never commit .env.
The repository includes .gitignore rules that exclude it.
Project structure
nullstice-discord-client/
├── src/
│   └── main/
│       └── java/
│           └── com/
│               └── nullstice/
│                   ├── Main.java
│                   ├── bot/
│                   ├── commands/
│                   ├── api/
│                   ├── models/
│                   └── ui/
│
├── .env
├── .gitignore
├── README.md
└── build.gradle

Project scope
This project is only the Discord client.
The following are intentionally outside the client:
- Backend development
- Database management
- Challenge generation
- Answer generation
- Answer validation
- Point calculation
- Account persistence
- Backend administration
The client communicates with the backend through its documented API.
Status
🚧 Currently in development.
The project will be implemented incrementally, starting with the Java/JDA
bot foundation and API client.