# Eventify - Google Calendar & Discord Event Manager

**Eventify** is a Node.js application for managing events in Google Calendar and sending event reminders on Discord. This project has two parts: Part 1 covers Google Calendar integration with CRUD operations, and Part 2 covers Discord integration for event reminders.



## Part 1: Google Calendar Integration (CRUD Operations)

### Objective
Integrate Google Calendar API to enable users to create, read, update, and delete events from the application. This requires setting up Google OAuth for authentication, implementing CRUD functions, and configuring API routes.

### Prerequisites
- **Node.js and npm**
- **Google Cloud Account**
- **Google Calendar API enabled**
- **Postman** (for testing endpoints)

---

### Instructions

#### Step 1: Set Up a Google Cloud Project and Enable Google Calendar API

1. **Create a New Google Cloud Project**:
   - Go to the [Google Cloud Console](https://console.cloud.google.com/).
   - Click on **Select a Project** on top header > **New Project**.
   - Name the project **Eventify Calendar Manager** and create it.

2. **Enable Google Calendar API**:
   - In the Cloud Console, go to **APIs & Services > Library**.
   - Search for **Google Calendar API** and enable it for your project.

3. **Create OAuth Credentials**:
   - Go to **APIs & Services > Credentials**.
   - Click **Create Credentials** > **OAuth client ID**.
   - Configure the **OAuth consent screen**:
     - Set **App Name** (e.g., **Eventify App**) and fill in the required fields.
     - Set **Scopes** to include `https://www.googleapis.com/auth/calendar`.
   - Create **OAuth 2.0 Client ID** credentials for the application.
   - Download the credentials JSON file and save it in the project’s `config` directory as `credentials.json`.

#### Step 2: Configure OAuth 2.0 Authentication with Google APIs

1. **Environment Configuration**:
   - Create a `.env` file to store sensitive information:
     ```plaintext
     PORT=3000
     ```

2. **Set Up OAuth Client**:
   - Load credentials from `credentials.json`.
   - Configure OAuth2 client to manage authentication and generate an authorization URL.
   - After authorization, store the token locally to avoid repeated logins.

#### Step 3: Implement CRUD Operations

1. **Create Event**:
   - Define a function to create a new event in Google Calendar.
   - Specify fields like summary, start and end times, description, and location.
   - Use `calendar.events.insert` to insert the event.

2. **Read Events**:
   - Define a function to fetch upcoming events.
   - Use `calendar.events.list` to retrieve a list of events within a specific time range.

3. **Update Event**:
   - Define a function to update an event’s details, such as time or description, using the `eventId`.
   - Use `calendar.events.update` for updating selected fields.

4. **Delete Event**:
   - Define a function to delete an event by its `eventId`.
   - Use `calendar.events.delete` to remove the event from Google Calendar.

#### Step 4: Set Up Routes for CRUD Operations

1. **Define Routes**:
   - Set up Express routes in `server.js` for each operation:
     - **GET /events**: Fetch upcoming events.
     - **POST /events**: Create a new event with data from the request body.
     - **PUT /events/:id**: Update an event by its ID.
     - **DELETE /events/:id**: Delete an event by its ID.

2. **Implement Route Handlers**:
   - In each route handler, call the relevant CRUD function with required parameters.

#### Step 5: Testing and Validation

1. **API Testing**:
   - Use Postman to test each route by sending requests for `GET`, `POST`, `PUT`, and `DELETE` operations.

2. **Verify Calendar Changes**:
   - Check Google Calendar to confirm that events are added, modified, and deleted as expected.

---


## Part 2: Discord Integration (Event Reminders)

### Objective
Integrate the Discord API to send event reminders in a designated channel shortly before each event begins. This involves setting up a Discord bot, configuring message permissions, scheduling reminders, and linking reminders to events managed in Google Calendar.



### Prerequisites
- **Discord Account** with server permissions (to add and configure the bot, create new server if don't have permission for existing ones).
- **Discord Developer Portal Access** to create and manage the bot.

---

### Instructions

#### Step 1: Set Up a Discord Bot

1. **Create a New Bot**:
   - Go to the [Discord Developer Portal](https://discord.com/developers/applications).
   - Click **New Application** and name it (e.g., **Eventify Reminder Bot**).
   - In the left menu, go to **Bot** and click **Add Bot** to create a bot for this application.
   - Copy the **Token** for your bot from the Bot page (store it securely as you’ll need it in your code).

2. **Configure Bot Permissions**:
   - Under the **OAuth2** section in the left menu, select **URL Generator**.
   - Under **OAuth2 URL Generator**:
     - Select **bot** as the scope.
     - Under **Bot Permissions**, check **Send Messages**, **Read Messages/View Channels**, and **Manage Messages**.
   - Copy the generated OAuth2 URL and paste it into your browser to invite the bot to your Discord server.

3. **Set Up Environment Variables**:
   - In your `.env` file, add the bot token:
     ```plaintext
     DISCORD_BOT_TOKEN=YOUR_DISCORD_BOT_TOKEN
     ```

#### Step 2: Configure the Discord Bot in Your Project

1. **Install Discord.js**:
   - Install `discord.js` to interact with Discord’s API:
     ```bash
     npm install discord.js
     ```

2. **Initialize the Discord Bot**:
   - In your project, create a new file (e.g., `src/discordBot.js`) and set up a basic connection to Discord.
   - Configure the bot to connect to your server using the `DISCORD_BOT_TOKEN` from your `.env` file.
   - Add an event listener for when the bot is ready to ensure it connects correctly.

3. **Configure Channel Access**:
   - Identify the ID of the Discord channel where reminders will be sent.
   - You can find the channel ID by right-clicking the channel in Discord and selecting **Copy ID** (enable Developer Mode in Discord settings if needed).
   - Add the **Channel ID** to your `.env` file:
     ```plaintext
     DISCORD_CHANNEL_ID=YOUR_CHANNEL_ID
     ```

#### Step 3: Scheduling Reminders

1. **Set Up Event Time Checking**:
   - Create a function to check upcoming Google Calendar events at regular intervals.
   - Use a scheduling library like `node-schedule` or `cron` to run this check periodically (e.g., every 5 minutes).
   - For each event, calculate the time difference between the current time and the event’s start time.

2. **Identify Events for Reminders**:
   - If an event is scheduled to start within the next 5 minutes, trigger a reminder.
   - Store the `eventId` of each notified event to avoid duplicate reminders.

#### Step 4: Send Reminder Messages on Discord

1. **Format the Reminder Message**:
   - Define the content of the reminder message. Include:
     - Event name
     - Start time
     - Any additional details (e.g., location, description)

2. **Send Message to Discord Channel**:
   - Use the `discord.js` library to send the formatted message to the channel specified by `DISCORD_CHANNEL_ID`.
   - The bot should send the reminder message only once per event.

#### Step 5: Testing and Validation

1. **Testing Reminders**:
   - Create test events in Google Calendar with start times a few minutes from now.
   - Verify that the bot sends reminder messages in the specified Discord channel 5 minutes before each event.
   - Adjust event times in Google Calendar to test for different scenarios (e.g., events starting in less than 5 minutes).

2. **Error Handling**:
   - Ensure the bot handles cases where it cannot access Google Calendar (e.g., due to token expiration).
   - Add error handling for scenarios where the bot cannot post to Discord (e.g., channel permissions issues).

