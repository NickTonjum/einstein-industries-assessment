<template>
	<div class="frow direction-row">
		<div class="frow width-100">
			<h3 class="text-center mb-10">Nicolas Tonjum EI Assessment</h3>
		</div>
		<div class="frow width-100 row-center mt-20 mb-30">
			<p>Value for</p>
			<span class="mx-10">
				<select name="options" id="optionSelect" v-model="option">
					<option v-for="(item, key) in JSONValue" v-bind:key="key" :value="item">{{key}}</option>
				</select>
			</span>
			<p>is</p>
			<h4 class="mx-10">{{option}}</h4>
			<p>with a data type of</p>
			<h4 class="ml-10">{{testType(option)}}.</h4>
		</div>
		<div class="frow row-evenly width-100">
			<div class="codejar-container frow direction-column">
				<h5 class="mb-5 text-center">Valid Config File (Editable, Please test as you wish)</h5>
				<code-jar :value="inputValue" @input="onInput" code-style="background:white;" lang="js" />
			</div>
			<div class="button-container frow direction-column text-center">
				<i class="material-icons">arrow_forward</i>
				<p class="my-10">Automatic Conversion</p>
				<i class="material-icons">arrow_forward</i>
			</div>
			<div class="codejar-container frow direction-column">
				<h5 class="mb-5 text-center">JSON Format (Read Only)</h5>
				<code-jar id="codeJarJSON" :value="this.createJSONString(JSONValue)" readonly code-style="background:white;" lang="json" />
			</div>
		</div>
	</div>
</template>

<script>
import CodeJar from 'vue-codejar';

export default {
	components: { CodeJar },
	data() {
		return {
			inputValue: `# This is what a comment looks like, ignore it` +
				`\n# All these config lines are valid` +
				`\nhost = test.com` +
				`\nserver_id=55331` +
				`\nserver_load_alarm=2.5` +
				`\nuser= user` +
				`\n# comment can appear here as well` +
				`\nverbose =true` +
				`\ntest_mode = on` +
				`\ndebug_mode = off` +
				`\nlog_file_path = /tmp/logfile.log` +
				`\nsend_notifications = yes`,
			JSONValue: {},
			option: `''`
		};
	},
	mounted() {
		// Manually convert with initial text
		this.onInput(this.inputValue);
	},
	methods: {
		onInput(code) {
			// Only convert if parameter is of type string
			if (typeof code === 'string') {
				// Set value to JSON code editor by converting text to JSON
				this.JSONValue = this.convertToJSON(code);
			}
		},
		convertToJSON(text) {
			// Create new object for JSON
			let jsonObject = {};
			text.split(/\n/).forEach(line => {
				// Replace all white spaces with nothing
				const noSpacesLine = line.replace(/\s/g, '');
				if (noSpacesLine.startsWith('#')) {
					return;
				}

				// Split on = so then we have the key and the data
				let parsedData = noSpacesLine.split('=');

				/***
				 * Deal with Boolean-like config values
				 * I was not sure how else to convert them rather than directly
				 */
				if (parsedData[1] === 'on') jsonObject[parsedData[0]] = true;
				else if (parsedData[1] === 'off') jsonObject[parsedData[0]] = false;
				else if (parsedData[1] === 'yes') jsonObject[parsedData[0]] = true;
				else if (parsedData[1] === 'no') jsonObject[parsedData[0]] = false;
				else jsonObject[parsedData[0]] = parsedData[1];
			})
			// Return JSON object
			return jsonObject;
		},
		testType(input) {
			try {
				// Try to parse so it will convert to number or boolean if needed
				input = JSON.parse(input);
			}
			finally {
				// Test if input is a Number, String, or Boolean
				if (typeof input === 'number') return 'Number';
				else if (typeof input === 'string') return 'String';
				else return 'Boolean';
			}
		},
		createJSONString(input) {
			if (input) {
				// Stringify input and replace pieces with new lines to format JSON
				input = JSON.stringify(input).replace(/,/g, ',\n\t');
				input = input.replace(/{/g, '{\n\t');
				input = input.replace(/}/g, '\n}');
				return input;
			}
		}
	},
};
</script>

<style scoped>
@import "../node_modules/frow/dist/frow.min.css";

.codejar-container {
	width: 45%;
}
.button-container {
	width: 7%;
}
#optionSelect {
	width: 150px;
	max-width: 150px;
}
</style>
