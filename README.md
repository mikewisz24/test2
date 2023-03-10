# test2


import requests

# Define the URL for the Microsoft Graph API's /me endpoint
ME_URL = 'https://graph.microsoft.com/v1.0/me'

# Define a list of email addresses or user object IDs for the authorized users
WHITELIST = [
    'user1@example.com',
    'user2@example.com',
    'user3@example.com',
    '...',
]

class AzureAdMsalOAuthenticator(AzureAdOAuthenticator):
    async def authenticate(self, handler, data=None):
        # ...

        # Check whether the access token is associated with a valid user in Azure AD
        if not self.is_valid_access_token(access_token):
            return None

        # Get information about the user from Azure AD using the access token
        user_info = self.get_user_info(access_token)

        # Verify that the user is authorized to use JupyterHub
        if not self.is_whitelisted(user_info):
            return None

        # ...

    def is_valid_access_token(self, access_token):
        # Define the headers for the HTTP request to the /me endpoint
        headers = {
            'Authorization': f'Bearer {access_token}',
            'Content-Type': 'application/json'
        }

        # Send an HTTP request to the /me endpoint
        response = requests.get(ME_URL, headers=headers)

        # Check the response status code to see if the access token is valid
        if response.status_code == 200:
            return True
        else:
            return False

    def get_user_info(self, access_token):
        # Define the headers for the HTTP request to the /me endpoint
        headers = {
            'Authorization': f'Bearer {access_token}',
            'Content-Type': 'application/json'
        }

        # Send an HTTP request to the /me endpoint
        response = requests.get(ME_URL, headers=headers)

        # Return the JSON data returned by the endpoint
        return response.json()

    def is_whitelisted(self, user_info):
        # Check if the user's email address or user object ID is in the whitelist
        email = user_info.get('mail', '').lower()
        user_id = user_info.get('id', '')
        return email in WHITELIST or user_id in WHITELIST
