ELEVENLABS AGENT SETUP : 

\- Select Blank Agent

System prompt :   
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

Tools : 

- click add tool  
- Click edit as Json  
- Copy the json code of the respective tools to setup the tool  
- For webhook tools use your n8n webhook url 

 

### **Tool 1: get\_user\_email**

**→ Retrieves the authenticated user's email and phone number from Google OAuth.**

**JSON :** 

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

###  **Tool 2: store\_conversation**

**→ Stores the full conversation along with user details and timestamp for future reference.**

**JSON :** 

**{**

  **"name": "store\_conversation",**

  **"description": "Store the conversation with user email for future reference",**

  **"assignments": \[\],**

  **"type": "webhook",**

  **"api\_schema": {**

    **"url": "https://kabilan20040521.app.n8n.cloud/webhook/store-conversation",**

    **"method": "POST",**

    **"path\_params\_schema": \[\],**

    **"query\_params\_schema": \[\],**

    **"request\_body\_schema": {**

      **"id": "body",**

      **"type": "object",**

      **"description": "Store conversation data with user authentication details for future reference and personalization.",**

      **"properties": \[**

        **{**

          **"id": "Agent\_id",**

          **"type": "string",**

          **"value\_type": "llm\_prompt",**

          **"description": "Agent Id of this agent",**

          **"dynamic\_variable": "",**

          **"constant\_value": "",**

          **"required": true**

        **},**

        **{**

          **"id": "user\_email",**

          **"type": "string",**

          **"value\_type": "llm\_prompt",**

          **"description": "The authenticated user's email address from Google OAuth",**

          **"dynamic\_variable": "",**

          **"constant\_value": "",**

          **"required": true**

        **},**

        **{**

          **"id": "phone\_number",**

          **"type": "string",**

          **"value\_type": "llm\_prompt",**

          **"description": "The contact number of the user",**

          **"dynamic\_variable": "",**

          **"constant\_value": "",**

          **"required": true**

        **},**

        **{**

          **"id": "Agent\_name",**

          **"type": "string",**

          **"value\_type": "llm\_prompt",**

          **"description": "Name of the agent (Here it is \\"Taki\\")",**

          **"dynamic\_variable": "",**

          **"constant\_value": "",**

          **"required": true**

        **},**

        **{**

          **"id": "conversation\_content",**

          **"type": "string",**

          **"value\_type": "llm\_prompt",**

          **"description": "Complete conversation transcript including user messages and AI responses",**

          **"dynamic\_variable": "",**

          **"constant\_value": "",**

          **"required": true**

        **},**

        **{**

          **"id": "conversation\_id",**

          **"type": "string",**

          **"value\_type": "llm\_prompt",**

          **"description": "Unique session identifier for this conversation",**

          **"dynamic\_variable": "",**

          **"constant\_value": "",**

          **"required": true**

        **},**

        **{**

          **"id": "timestamp",**

          **"type": "string",**

          **"value\_type": "llm\_prompt",**

          **"description": "ISO 8601 formatted timestamp of when conversation occurred",**

          **"dynamic\_variable": "",**

          **"constant\_value": "",**

          **"required": true**

        **},**

        **{**

          **"id": "action",**

          **"type": "string",**

          **"value\_type": "llm\_prompt",**

          **"description": "Action to perform: 'store' or 'get'",**

          **"dynamic\_variable": "",**

          **"constant\_value": "",**

          **"required": true**

        **}**

      **\],**

      **"required": false,**

      **"value\_type": "llm\_prompt"**

    **},**

    **"request\_headers": \[\],**

    **"auth\_connection": null**

  **},**

  **"response\_timeout\_secs": 20,**

  **"dynamic\_variables": {**

    **"dynamic\_variable\_placeholders": {}**

  **}**

**}**

### **Tool 3: check\_user**

**→ Checks if the user already exists in the database using their email.**

JSON : 

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

### **Tool 4: store\_user**

**→ Stores the user's phone number and email when they are new.**

Json : 
'''json
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
'''
### **Tool 5: get\_conversation\_history**

**→ Retrieves the user's past conversation history using their email.**

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

![][image1]

[image1]: <data:image/png;base64,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>
