---
sidebar_position: 2
---

# Installation

### Method 1 - Wally

1. Setup [Wally](https://wally.run/) by using `wally init`.
2. Add `howmanysmall/FastFzy` as a dependency.

```toml
[dependencies]
FastFzy = "howmanysmall/fast-fzy@^1.0.0"
```

### Method #2 - HttpService

This method uses `HttpService` to install FastFzy.

1. In Roblox Studio, paste the following command into your command bar.
2. Run the following command:

<textarea readonly rows="5" onClick={e => e.target.select()} style={{
   width: "100%"
}}>
   {`local ReplicatedStorage = game:GetService("ReplicatedStorage")
local HttpService = game:GetService("HttpService")
local HttpEnabled = HttpService.HttpEnabled
HttpService.HttpEnabled = true
type Result<T, E> = {
	Success: true,
	Value: T,
} | {
	Success: false,
	Error: E,
}
local function RequestAsync(RequestDictionary)
	return HttpService:RequestAsync(RequestDictionary)
end
local function GetAsync(Url, Headers): Result<string, string>
	Headers["cache-control"] = "no-cache"
	local Success, ResponseDictionary = pcall(RequestAsync, {
		Headers = Headers;
		Method = "GET";
		Url = Url;
	})
	if not Success then
		return {
			Success = false;
			Error = \`{ResponseDictionary}\`;
		}
	end
	if ResponseDictionary.Success then
		return {
			Success = true;
			Value = ResponseDictionary.Body;
		}
	end
	return {
		Success = false;
		Error = \`HTTP {ResponseDictionary.StatusCode}: {ResponseDictionary.StatusMessage}\`;
	}
end
local function Initify(Root)
	local InitFile = Root:FindFirstChild("init")
		or Root:FindFirstChild("init.lua") or Root:FindFirstChild("init.luau")
		or Root:FindFirstChild("init.client.lua") or Root:FindFirstChild("init.client.luau")
		or Root:FindFirstChild("init.server.lua") or Root:FindFirstChild("init.server.luau")
	if InitFile then
		InitFile.Name = Root.Name
		InitFile.Parent = Root.Parent
		for _, Child in Root:GetChildren() do
			Child.Parent = InitFile
		end
		Root:Destroy()
		Root = InitFile
	end
	for _, Child in Root:GetChildren() do
		Initify(Child)
	end
	return Root
end
local FilesJson = GetAsync(
	"https://api.github.com/repos/howmanysmall/fast-fzy/contents/src",
	{accept = "application/vnd.github.v3+json"}
)
if not FilesJson.Success then
	error(FilesJson.Error)
end
local FilesList = HttpService:JSONDecode(FilesJson.Value)
local FastFzy = Instance.new("Folder")
FastFzy.Name = "FastFzy"
for _, FileData in FilesList do
	local ModuleScript = Instance.new("ModuleScript")
	ModuleScript.Name = \`{string.match(FileData.name, "(%w+)%.luau?")}\`
	local Result = GetAsync(FileData.download_url, {})
	ModuleScript.Source = if Result.Success then \`{Result.Value}\` else \`--[====[{Result.Error}]====]\`
	ModuleScript.Parent = FastFzy
end
FastFzy.Parent = ReplicatedStorage
Initify(FastFzy)
HttpService.HttpEnabled = HttpEnabled`}
</textarea>


## Next

Now, check out the [API reference](/api/FastFzy)!
