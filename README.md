# ptoh_webhooks
This repository solely exists to let [PToH](https://www.roblox.com/games/7593639579/) send webhook messages to the Discord server.
`hooks.hyra.io` used to be the bridge between the two, but it stopped working around 2 months before this repository was created.

## How to use this setup for your own Roblox game
1. Create a copy of this repository
   - Make sure your repository is public, otherwise you will be limited in the amount of Actions minutes/month you can use
   - Keep in mind that with a public repository, people can see what kind of messages are being sent to a channel regardless of whether or not they have access to it
3. Create repository secrets for each webhook link you will use
   - You can make one by going to `Settings > Secrets and Variables > Actions`
4. Edit `.github/workflows/discord.yml` and make your own steps using the following format:
```yml
- name: <Could be anything really>
  if: ${{ inputs.channel == '<Channel Name>' }}
  run: wget -q -O- --header="Content-Type:application/json" --post-data='${{ inputs.data }}' ${{ secrets.<The Secret's Name> }}
```
5. Create a [fine-grained personal access token](https://github.com/settings/tokens?type=beta) and use the following settings:
   - Expiration: As late as possible
   - Repository Access: Only select repositories, select the webhook repository
   - Repository Permissions: Set Actions to Read & Write
6. Copy the access token that gets generated and save it somewhere safe, because you won't be able to see it again
7. Paste this code somewhere inside a server script
```lua
local http = game:GetService("HttpService")

http:RequestAsync{
  Url = "https://api.github.com/repos/<Your Username>/<Your Repository Name>/actions/workflows/discord.yml/dispatches",
  Method = "POST",
  Headers = {
    Accept = "application/vnd.github+json",
    Authorization = "Bearer <Your Access Token>",
    ["X-GitHub-Api-Version"] = "2022-11-28"
  },
  Body = http:JSONEncode{
    ref = "main",
    inputs = {
      channel = "<Channel Name>",
      data = http:JSONEncode{content = "<Message>"}:gsub("'",[['\'']])
    }
  }
}
```
