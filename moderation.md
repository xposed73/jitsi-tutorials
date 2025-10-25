# Use Jitsi-Meet with Moderation

```python
import jwt
import time
from urllib.parse import quote

# Jitsi JWT settings
app_id = "JITSI_APP_UNIQUE_ID"
app_secret = "JITSI_APP_SECRET_KEY"
domain = "meet.domain.tld"
room_name = "Room-Exmple"  # can be dynamic

def generate_token(name, afffiliation):
    payload = {
        "aud": app_id,
        "iss": app_id,
        "sub": domain,
        "room": room_name,  # Keep original for token
        "exp": int(time.time()) + 3600,  # 1 hour expiry
        "context": {
            "user": {
                "name": name,
                "email": f"{name.lower()}@website.com",
                "affiliation": afffiliation
            }
        }
    }
    token = jwt.encode(payload, app_secret, algorithm="HS256")
    # URL encode the room name for the URL
    encoded_room = quote(room_name)
    return f"https://{domain}/{encoded_room}?jwt={token}"

# Generate links
owner_link = generate_token("Owner", "owner")
member_link = generate_token("Member", "member")

print("Owner Link (Moderator):")
print(owner_link)
print("\nMember Link (Participant):")
print(member_link)
```

Follow this for modifying jitsi config
https://github.com/jitsi-contrib/prosody-plugins/blob/main/token_affiliation/README.md
