ELEVENLABS AGENT SETUP : 

Agents\- Select Blank Agent

System prompt :   
```
You are Taki, a voice-based AI assistant with memory and user identification.  
User Authentication & User Check  
At the start of every conversation, automatically and silently call the get\_user\_email tool to retrieve the user's email address.  
The get\_user\_email tool returns a JSON string. Parse this JSON to extract the user\_email field for use in other tools.  
Immediately after retrieving the email, call the check\_user tool with the user\_email to check if the user already exists in the database.  
If the user exists (as indicated by the check\_user tool response), do not ask for their phone number again.  
If the user does not exist (as indicated by the check\_user tool response):  
Politely and conversationally ask the user for their phone number.  
Use language such as:  
“To personalize your experience, could you please share your phone number?”  
or  
“May I have your phone number to help serve you better? Your information will be kept private.”  
Do not use robotic or system-like language such as “I do not have access to the user's phone number.”  
After receiving the phone number from the user, immediately call the store\_user tool to save the new user with their email and phone number in the database.  
Never ask the user for their email directly; always use the get\_user\_email tool.  

Conversation Storage

At the end of each conversation, call the store\_conversation tool with the following fields:  
user\_email: The value returned by get\_user\_email.  
phone\_number: The phone number provided by the user (if the user is new and was asked for it; otherwise, use the previously stored number).  
conversation\_content: The full transcript of the conversation, including both user and AI messages.  
conversation\_id: Generate a unique ID in the format conv\_YYYYMMDD\_HHMMSS\_user, using the current date/time and the user's email prefix (before the @) or "user" if not available.  
timestamp: The current time in ISO 8601 format.  
action: Always set this to "store" when saving a conversation.  

Agent\_name: Taki  
Agent\_id: agent\_01jzvrgx8pff7btgjfwb8js84h

Never ask the user for their email. Only ask for their phone number if the user is new and not found in the database.  
Conversation Retrieval  
If the user asks to see, review, or retrieve their past conversations or conversation history, call the get\_conversation\_history tool.  
If the user asks for personal information about themselves (e.g., their age, name, etc.), automatically call the get\_conversation\_history tool.  
Pass the user\_email value obtained from the get\_user\_email tool as the parameter.  
Never ask the user for their email; always retrieve it automatically.  
Present the retrieved conversation history in a concise and user-friendly way.  
Do not expose or share the user's email or phone number in any response unless the user specifically asks for it.  
Interaction Guidelines  
All user identification and memory actions must be handled via tools and system context, never by asking the user (except for the phone number if the user is new).  
Never expose or share the user's email or phone number in any response unless the user specifically asks for it.  
Use the email and phone number only for internal memory, personalization, and linking previous conversations.  
If you cannot retrieve the email, proceed with "No user email available" but do not ask the user for their email.  
Privacy  
Respect user privacy at all times. Do not share or expose the email or phone number in any response unless the user requests it.  
Only use the email and phone number for linking and recalling previous conversations.  
Summary  
All actions related to user identification and memory must be silent and automatic, except when the user asks for their email or phone number, or when a new user must provide their phone number.  
Never prompt the user for their email, conversation content, conversation ID, or timestamp.  
Always use the get\_user\_email, check\_user, store\_user, store\_conversation, and get\_conversation\_history tools as described above.  
When the user asks about something they may have shared before, search their conversation history and use it to answer if possible.

```
Tools : 

- click add tool  
- Click edit as Json  
- Copy the json code of the respective tools to setup the tool  
- For webhook tools use your n8n webhook url 

 

### **Tool 1: get\_user\_email**

**→ Retrieves the authenticated user's email and phone number from Google OAuth.**

**JSON :** 
```
{  
  "name": "get\_user\_email",  
  "description": "Retrieves the email address and phone number of the authenticated user from Google Auth",  
  "assignments": \[\],  
  "type": "client",  
  "expects\_response": true,  
  "response\_timeout\_secs": 6,  
  "parameters": \[\],  
  "dynamic\_variables": {  
    "dynamic\_variable\_placeholders": {}  
  }  
}
```
###  **Tool 2: store\_conversation**

**→ Stores the full conversation along with user details and timestamp for future reference.**

**JSON :** 
```
{
  "name": "store_conversation",
  "description": "Store the conversation with user email for future reference",
  "assignments": [],
  "type": "webhook",
  "api_schema": {
    "url": "https://kabilan20040521.app.n8n.cloud/webhook/store-conversation",
    "method": "POST",
    "path_params_schema": [],
    "query_params_schema": [],
    "request_body_schema": {
      "id": "body",
      "type": "object",
      "description": "Store conversation data with user authentication details for future reference and personalization.",
      "properties": [
        {
          "id": "conversation_content",
          "type": "string",
          "value_type": "llm_prompt",
          "description": "Complete conversation transcript including user messages and AI responses",
          "dynamic_variable": "",
          "constant_value": "",
          "required": true
        },
        {
          "id": "user_email",
          "type": "string",
          "value_type": "llm_prompt",
          "description": "The authenticated user's email address from Google OAuth",
          "dynamic_variable": "",
          "constant_value": "",
          "required": true
        },
        {
          "id": "Agent_id",
          "type": "string",
          "value_type": "llm_prompt",
          "description": "Agent Id of this agent",
          "dynamic_variable": "",
          "constant_value": "",
          "required": true
        }
      ],
      "required": false,
      "value_type": "llm_prompt"
    },
    "request_headers": [],
    "auth_connection": null
  },
  "response_timeout_secs": 20,
  "dynamic_variables": {
    "dynamic_variable_placeholders": {}
  }
}
```

### **Tool 3: check\_user**

**→ Checks if the user already exists in the database using their email.**

JSON : 
```
{  
  "name": "check\_user",  
  "description": "Use this tool to check the customer",  
  "assignments": \[\],  
  "type": "webhook",  
  "api\_schema": {  
    "url": "https://kabilan20040521.app.n8n.cloud/webhook/check\_user",  
    "method": "POST",  
    "path\_params\_schema": \[\],  
    "query\_params\_schema": \[\],  
    "request\_body\_schema": {  
      "id": "body",  
      "type": "object",  
      "description": "Extract the email id of the user using get\_user\_email tool",  
      "properties": \[  
        {  
          "id": "user\_email",  
          "type": "string",  
          "value\_type": "llm\_prompt",  
          "description": "The email id of the user",  
          "dynamic\_variable": "",  
          "constant\_value": "",  
          "required": true  
        }  
      \],  
      "required": false,  
      "value\_type": "llm\_prompt"  
    },  
    "request\_headers": \[\],  
    "auth\_connection": null  
  },  
  "response\_timeout\_secs": 20,  
  "dynamic\_variables": {  
    "dynamic\_variable\_placeholders": {}  
  }  
}
```
### **Tool 4: store\_user**

**→ Stores the user's phone number and email when they are new.**

Json : 
```
{  
  "name": "store\_user",  
  "description": "Use this tool to store the user details ",  
  "assignments": \[\],  
  "type": "webhook",  
  "api\_schema": {  
    "url": "https://kabilan20040521.app.n8n.cloud/webhook/store\_user",  
    "method": "POST",  
    "path\_params\_schema": \[\],  
    "query\_params\_schema": \[\],  
    "request\_body\_schema": {  
      "id": "body",  
      "type": "object",  
      "description": "Parse the extracted email and the phone number of the user",  
      "properties": \[  
        {  
          "id": "phone\_number",  
          "type": "string",  
          "value\_type": "llm\_prompt",  
          "description": "Phone number of the user that is extracted from the user",  
          "dynamic\_variable": "",  
          "constant\_value": "",  
          "required": true  
        },  
        {  
          "id": "email",  
          "type": "string",  
          "value\_type": "llm\_prompt",  
          "description": "The email id of the user extracted using get\_user\_email tool",  
          "dynamic\_variable": "",  
          "constant\_value": "",  
          "required": true  
        }  
      \],  
      "required": false,  
      "value\_type": "llm\_prompt"  
    },  
    "request\_headers": \[  
      {  
        "type": "value",  
        "name": "Content-Type",  
        "value": "application/json"  
      }  
    \],  
    "auth\_connection": null  
  },  
  "response\_timeout\_secs": 20,  
  "dynamic\_variables": {  
    "dynamic\_variable\_placeholders": {}  
  }  
}
```
### **Tool 5: get\_conversation\_history**

**→ Retrieves the user's past conversation history using their email.**
```
{  
  "name": "get\_conversation\_history",  
  "description": "Retrieve the conversation history for the authenticated user",  
  "assignments": \[\],  
  "type": "webhook",  
  "api\_schema": {  
    "url": "https://kabilan20040521.app.n8n.cloud/webhook/get\_conversation\_history",  
    "method": "POST",  
    "path\_params\_schema": \[\],  
    "query\_params\_schema": \[\],  
    "request\_body\_schema": {  
      "id": "body",  
      "type": "object",  
      "description": "Provide user email to fetch their stored conversation history",  
      "properties": \[  
        {  
          "id": "user\_email",  
          "type": "string",  
          "value\_type": "llm\_prompt",  
          "description": "Email of the user whose conversation history needs to be retrieved",  
          "dynamic\_variable": "",  
          "constant\_value": "",  
          "required": true  
        }  
      \],  
      "required": false,  
      "value\_type": "llm\_prompt"  
    },  
    "request\_headers": \[\],  
    "auth\_connection": null  
  },  
  "response\_timeout\_secs": 20,  
  "dynamic\_variables": {  
    "dynamic\_variable\_placeholders": {}  
  }  
}
```
WEBHOOKS : 

Apart from adding the webhook url in the tools settings , You should add the webhook urls in the webhook setting of the elevenlabs too..

N8N WORKFLOWS : 

**1\. check\_user Workflow**

**Purpose**:check whether the user details is already there in the database or not  

**2\. store\_user Workflow**

**Purpose**: Saves new users' phone numbers and email addresses in the Users table if they don’t already exist.

**3\. store\_conversation Workflow**

**Purpose**: Stores a full conversation transcript in the conversations table for future reference.

**4\. get\_conversation\_history Workflow**

**Purpose**: Retrieves past conversations based on the authenticated user's email.

IT WILL BE REQUIRED TO ADD THE CREDENTIALS OF YOUR SUPABASE DATABASE IN N8N.

SUPABASE DATABASE SCHEMA : 
```sql
CREATE TABLE public.Agent\_details (

  id bigint GENERATED ALWAYS AS IDENTITY NOT NULL,

  created\_at timestamp with time zone NOT NULL DEFAULT now(),

  Agent\_id character varying UNIQUE,

  Agent\_name text,

  CONSTRAINT Agent\_details\_pkey PRIMARY KEY (id)

);

CREATE TABLE public.Users (

  created\_at timestamp with time zone NOT NULL DEFAULT now(),

  email text UNIQUE,

  phone\_number text,

  User\_id uuid NOT NULL DEFAULT gen\_random\_uuid() UNIQUE,

  CONSTRAINT Users\_pkey PRIMARY KEY (User\_id)

);

CREATE TABLE public.conversations (

  id integer GENERATED ALWAYS AS IDENTITY NOT NULL,

  conversation\_id character varying NOT NULL DEFAULT gen\_random\_uuid() UNIQUE,

  conversation\_content text NOT NULL,

  created\_at timestamp without time zone DEFAULT CURRENT\_TIMESTAMP,

  User\_id uuid,

  Agent\_id character varying,

  CONSTRAINT conversations\_pkey PRIMARY KEY (id),

  CONSTRAINT conversations\_Agent\_id\_fkey FOREIGN KEY (Agent\_id) REFERENCES public.Agent\_details(Agent\_id),

  CONSTRAINT conversations\_User\_id\_fkey FOREIGN KEY (User\_id) REFERENCES public.Users(User\_id)

);
```
