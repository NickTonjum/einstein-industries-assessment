# einstein-industries-assessment

## Getting Started

1. Run `npm install`.
2. Run `npm run dev`.
3. Open web browser with following local host link. It should show up in your terminal. Mine is http://127.0.0.1:5173/.

## Explanation of Assessment and My Approach

I decided to create a Vue application to show my knowledge of a front end framework. Most of the standard Vue pieces were left out such as routing and different components as all I needed was one page. In this single page, I utilized a vue-codejar library: https://www.npmjs.com/package/vue-codejar, which allowed me to create two editors to show the code. I decided to use this package because I wanted to use a code editor and from my research I knew this had a very little size compared to others so it would not impact performance. The left editor is the config file which is editable and can be messed around with however you wish. The right side is a read-only editor which shows the JSON format of the config file. I decided to convert to JSON as my approch because it seemed the most appropriate. The conversion from the config file to the read-only editor is done automatically as you change values inside the editor. At the top of the page below the title, there is an example of using the JSON object on the page by selecting a key, and it will show you the value for that key and the data type. The option dropdown is automatically updated as you change the left side editor config file. I also utilized a css library: https://frowcss.com, which I have used before and I like how easy it makes adjusting page layouts.

As far as the assessment, most of my work is done in the src/App.vue file. I initialized the left side config file editor to the base config file that was sent to me and then computed the right side editor (JSON) with the proper JSON format. As the left side editor is edited, it will automatically determine the correct JSON format. My code assumes that the config file will be available as a string and it can parse it that way. Also, I decided to ignore comments.

Most if not all of the actual parsing tool can be found within the function convertToJSON. This takes in a string and then strips out the white space, and retrieves the key and value from splitting the string on the equals sign (=). Here is that function:

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
    }

## Explanation of App.vue code

In order to more understand how this works, I decided to explain the path of my App.vue file code.

At the top of the file, I have my HTML which contains a header for my project, then a div with a select that loops through the items in the JSON object, and a div container two code-jar editors. The code-jar editors get their value from the value property on its element. The value property points to a page-wide variable of which you can manipulate and that is how I change the values of the editors.

In the middle of the file is the script tag which contains the import for the code-jar component and the export for the Vue component. I have 3 private variables of which the entire page has access to. I used a mounted lifecycle hook to initialize the editor with the first config file. Then I have 3 methods: one for when the input changes in the editor, one to convert the value into JSON, and one to test the type of an input.

Lastly, we have our style tag which contains the import of the frow css package and other css styles I added.

## Assignment (Sent to me):

Please create a parsing tool that takes the example config file (provided below) and turns it into a usable object
in the language of your choice (hash, JSON object, associative array, class, etc).
The instructions for this are below. Please let us know if you have any questions! 

1. Do not use existing "complete" configuration parsing libraries/functions, we want to see how you would write the code to do this. 

2. Use of core and stdlib functions/objects such as string manipulation, regular expressions, etc is ok. 

3. We should be able to get the values of the config parameters in code, via their name.
How this is done specifically is up to you. 

4. Boolean-like config values (on/off, yes/no, true/false) should return real booleans: true/false. 

5. Numeric config values should return real numerics: integers, doubles, etc 

6. Ignore or error out on invalid config lines, your choice. 

7. Please include a short example usage of your code so we can see how you call it/etc. 

8. Push your work to a public git repository (github, bitbucket, etc) and send us the link. 

Valid Config File

    # This is what a comment looks like, ignore it
    # All these config lines are valid
    host = test.com
    server_id=55331
    server_load_alarm=2.5
    user= user
    # comment can appear here as well
    verbose =true
    test_mode = on
    debug_mode = off
    log_file_path = /tmp/logfile.log
    send_notifications = yes