npm install kb2abot
yarn add kb2abot
pnpm add kb2abot
import {Command} from "kb2abot"
const khoakomlemID = "100007723935647"

class Report extends Command {
	keywords = ["report", "bug"];
	description = "report bug/error to admin";
	guide = "<message>";
	permission = {
		"*": "*"
	};

	// Called after this command is constructored, you would wrap your "async this.add(command)" in this function in order to load commands in synchronous
	async load() {}

	// Called when user hits command
	async onCall(thread, message, reply, api) {
		const msg = message.args[0]
		if (msg.length > 0) {
			await api.sendMessage(`"${msg}"\n\n-ID: ${message.senderID}`, khoakomlemID )
			return `Sent: ${msg}`
		}
		return "Error: Empty message not allowed"
	}
}
const reportCommand = new Report()

import {readFileSync} from "fs"
import {resolve} from "url"
import {Plugin} from "kb2abot"

class MyPlugin extends Plugin {
	package = JSON.parse(
		readFileSync(new URL(resolve(import.meta.url, "package.json")))
	);

	// Called after this plugin is constructored (you would wrap your "async this.commands.add(command)" in this function in order to load commands in synchronous)
	async load() {
		// Add report command to this plugin
		await this.commands.add(reportCommand)
	}

	// Called when this plugin is disabled
	async onDisable() {}

	// Called when this plugin is enabled
	async onEnable() {}
}
const myPlugin = new MyPlugin()
import {PluginManager} from "kb2abot"
const configDirectory = "./config"
const userdataDirectory = "./userdata" // relative to cwd
const pluginManager = new PluginManager(configDirectory, userdataDirectory)
await pluginManager.add(myPlugin)
import {Deploy, Datastore} from "kb2abot"
import {readHJSON} from "kb2abot/util/common"
Datastore.init("./datastores") // If you dont init datastore, your bot will be freeze and throw timeout error
const botOptions = readHJSON("./bot.hjson") // Read and parse bot.hjson file (relative to cwd)
const client = await Deploy.facebook(botOptions.credential, {
	apiOptions: botOptions.fcaOptions,
	pluginManager: pluginManager
})
