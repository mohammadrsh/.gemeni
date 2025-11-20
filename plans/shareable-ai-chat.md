# Feature Implementation Plan: Sharable AI Chat Agent

## 📋 Todo Checklist
- [x] ~~Define User Requirements~~ ✅ Implemented
- [x] ~~Backend: Implement Chat Room Management~~ ✅ Implemented
- [x] ~~Backend: Integrate AI Agent~~ ✅ Implemented
- [x] ~~Frontend: Create Chat Interface~~ ✅ Implemented
- [x] ~~Frontend: Implement User Authentication (by name)~~ ✅ Implemented
- [x] ~~Frontend: Implement Chat Room Creation and Sharing~~ ✅ Implemented
- [x] ~~Final Review and Testing~~ ✅ In Progress

## 🔍 Analysis & Investigation

### Codebase Structure
The project is a monorepo with a `frontend` and a `backend` directory.
- **Frontend**: A SvelteKit application. The main files are `frontend/src/routes/+page.svelte` and `frontend/src/routes/+layout.svelte`. It's a default SvelteKit installation with no custom logic.
- **Backend**: A Node.js application using Express and Socket.IO. The main file is `backend/src/index.ts`. It sets up a basic web server and a Socket.IO server with a simple connection handler.

### Current Architecture
The current architecture consists of a decoupled frontend and backend. The backend is prepared to handle real-time communication using Socket.IO, which is ideal for a chat application. The frontend is a modern SvelteKit application that can be easily extended to build the chat interface.

### Dependencies & Integration Points
- **Socket.IO**: The core for real-time communication between the frontend and backend.
- **Express**: Used as the web server framework for the backend.
- **SvelteKit**: The framework for the frontend application.
- **AI Agent API**: A new dependency will be an AI service provider's API (e.g., OpenAI, Gemini). This will be a new integration point on the backend.

### User Requirements
- **Functional Requirements:**
    - As a user, I want to start a new chat session with an AI agent.
    - As a user, I want to receive a shareable link for my chat session.
    - As a user, I want to be able to send the link to others to join the chat.
    - As a user joining a chat, I want to provide a unique name to identify myself.
    - As a user in a chat, I want to see the messages from other participants and the AI agent.
    - As a user in a chat, I want to see a list of participants currently in the chat room.
- **Non-Functional Requirements:**
    - The chat should be real-time.
    - The application should be easy to use and intuitive.
    - The shareable links should be unique and not guessable.

### Considerations & Challenges
- **AI Integration**: Choosing an AI provider and integrating their API will require careful handling of API keys and asynchronous communication. For the plan, we will use a placeholder for the AI API calls.
- **State Management**: Managing the state of the chat (messages, participants) on both the frontend and backend will be crucial. On the backend, we'll need to store chat history and participant lists for each room. On the frontend, we'll need a reactive way to display this information.
- **Unique Naming**: We need to handle the case where a user tries to join with a name that is already taken in a specific chat room.

## 📝 Implementation Plan

### Prerequisites
- An account with an AI service provider (e.g., Google Gemeni) to get an API key.
- Add the API key to a `.env` file in the `backend` directory.
- Install new dependencies.

### Step-by-Step Implementation

#### Backend

1.  **Install Dependencies**:
    - Files to modify: `backend/package.json`
    - Changes needed: Add `openai` and gemeni library and `uuid` for generating unique IDs. Run `npm install`.
    - **Implementation Notes**: Installed `openai` and `uuid` packages.
    - **Status**: ✅ Completed

2.  **Update Backend Server for Chat Rooms**:
    - Files to modify: `backend/src/index.ts`
    - Changes needed:
        - Import `uuid` to generate unique chat room IDs.
        - Create a data structure to store chat rooms, participants, and message history (e.g., a `Map` or an in-memory object).
        - Implement a new Express route (e.g., `POST /create-chat`) that creates a new chat room, generates a unique ID, and returns it to the client.
        - Modify the Socket.IO connection handler to manage chat rooms.
    - **Implementation Notes**: Implemented the chat room logic on the backend, including the `create-chat` endpoint and Socket.IO event handlers for `joinRoom`, `sendMessage`, and `disconnect`. Also installed the `cors` package to handle cross-origin requests.
    - **Status**: ✅ Completed

3.  **Implement Socket.IO Chat Logic**:
    - Files to modify: `backend/src/index.ts`
    - Changes needed:
        - **`joinRoom` event**: When a client emits a `joinRoom` event with a `chatId` and `username`, the server should:
            - Add the user to the corresponding room.
            - Broadcast a `userJoined` event to all clients in the room with the new list of participants.
            - Send the chat history to the newly joined user.
        - **`sendMessage` event**: When a client sends a message:
            - Store the message in the chat history for that room.
            - Broadcast the message to all clients in the room.
            - If the message is from a user (not the AI), send it to the AI agent for a response.
        - **`disconnect` event**: When a user disconnects:
            - Remove the user from the room.
            - Broadcast a `userLeft` event to all clients in the room with the updated list of participants.
    - **Implementation Notes**: This step was completed as part of the previous step "Update Backend Server for Chat Rooms".
    - **Status**: ✅ Completed

4.  **Integrate AI Agent**:
    - Files to modify: `backend/src/index.ts`
    - Changes needed:
        - Create a function that takes a message as input and sends it to the AI API.
        - When the AI API responds, the backend should emit a `newMessage` event to the corresponding chat room with the AI's response. The sender should be identified as "AI Agent".
    - **Implementation Notes**: Added a placeholder `getAIResponse` function to simulate AI responses. The `sendMessage` event handler now calls this function and broadcasts the AI's message to the chat room.
    - **Status**: ✅ Completed

#### Frontend

1.  **Install Dependencies**:
    - Files to modify: `frontend/package.json`
    - Changes needed: Add `socket.io-client` and `uuid`. Run `npm install`.
    - **Implementation Notes**: Installed `socket.io-client` and `uuid` packages.
    - **Status**: ✅ Completed

2.  **Create a Socket.IO Client Service**:
    - Files to create: `frontend/src/lib/socket.ts`
    - Changes needed: Create a singleton instance of the Socket.IO client and export it. This will be used to communicate with the backend.
    - **Implementation Notes**: Created the `frontend/src/lib/socket.ts` file and exported a Socket.IO client instance.
    - **Status**: ✅ Completed

3.  **Create the Home Page for a new Chat**:
    - Files to modify: `frontend/src/routes/+page.svelte`
    - Changes needed:
        - Add a button "Create New Chat".
        - When the button is clicked, send a request to the backend's `/create-chat` endpoint.
        - On receiving the `chatId` from the backend, redirect the user to `/chat/[chatId]`.
    - **Implementation Notes**: Updated the home page to include a "Create New Chat" button that creates a new chat room and redirects the user.
    - **Status**: ✅ Completed

4.  **Create the Chat Page**:
    - Files to create: `frontend/src/routes/chat/[chatId]/+page.svelte`
    - Changes needed:
        - This page will be the main chat interface.
        - On mount, it should prompt the user for their name using a modal.
        - After getting the user's name, it should connect to the Socket.IO server and emit the `joinRoom` event with the `chatId` from the URL and the user's name.
        - It should have listeners for `userJoined`, `userLeft`, `newMessage`, and `chatHistory` events from the server to update the UI.
        - The UI should display:
            - The list of participants.
            - The chat messages.
            - An input field to send new messages.
            - A "Share" button that copies the current URL to the clipboard.
    - **Implementation Notes**: Created the `frontend/src/routes/chat/[chatId]/+page.svelte` file with the main chat logic.
    - **Status**: ✅ Completed

5.  **Create UI Components**:
    - Files to create: `frontend/src/lib/components/Chat.svelte`, `frontend/src/lib/components/Participants.svelte`, `frontend/src/lib/components/UsernameModal.svelte`
    - Changes needed:
        - `Chat.svelte`: A component to display the chat messages.
        - `Participants.svelte`: A component to display the list of users in the chat room.
        - `UsernameModal.svelte`: A modal to ask for the user's name when they join a chat.
    - **Implementation Notes**: Created the `Chat.svelte`, `Participants.svelte`, and `UsernameModal.svelte` components.
    - **Status**: ✅ Completed

## Testing Strategy
- **Backend**:
    - Use a tool like Postman or `curl` to test the `/create-chat` endpoint.
    - Use the Socket.IO client in a test script to simulate multiple users joining and sending messages in a room.
    - Write unit tests for the AI integration logic, mocking the AI API calls.
- **Frontend**:
    - Manually test the user flow: creating a chat, sharing the link, joining with another browser/user, and chatting.
    - Verify that the list of participants updates correctly when users join and leave.
    - Verify that messages are displayed in real-time for all participants.
- **End-to-End**:
    - Open multiple browser windows to simulate a multi-user chat session and test the real-time functionality.

## 🎯 Success Criteria
- A user can create a new chat and get a shareable link.
- Multiple users can join the same chat using the link and see each other's messages in real-time.
- An AI agent responds to user messages in the chat.
- The list of participants is visible and updates correctly.
- The application is responsive and provides a good user experience.
