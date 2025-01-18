# ⚠️ **Archived Repository**

This solution is outdated as of Jan 2025. This repository is no longer maintained and has been archived.

## Add Math Equations To Wix 

This repository provides a quick guide to adding [MathJax](https://www.mathjax.org/) support to Wix to enable both inline and regular equations without having an explicit iframe'd textbox. Most of this tutorial solidifies the instructions from [Zack Whitlock's blogpost](https://www.zackwhitlock.com/post/getting-mathjax-to-work-with-wix). 

- Add the following two code snippets to the code section of Wix. I only use them within blog posts, hence, they are only added to the post template. Make sure to add them to the `head`.
  - The first script essentially dynamically loads the MathJax library and configures it for your web page. It sets the configuration to load specific components, such as 'asciimath' input, 'lazy' user interface, 'chtml' output, and 'menu' user interface. It has an immediately-invoked function that actually appends the script to load MathJax to the head of the HTML file.
  - Unfortunately, since Wix adds this only when the webpage is loaded, MathJax can not directly parse and render the text in the body (as it has already been rendered). The second script sets up a MutationObserver to detect changes in the specified target node and triggers the MathJax typesetting process when certain types of mutations occur. The callback function gets executed when mutations are observed. It iterates through the list of mutations and, depending on the type of mutation, logs a message to the console and triggers the MathJax typesetting process using ` MathJax.typeset()`. We also have a method to check if the document is fully loaded. This part checks whether the document has fully loaded (document.readyState === 'complete'). If the document is fully loaded, it logs a message to the console, retrieves the target node with the ID 'content-wrapper', and starts observing it for mutations based on the previously defined configuration. 

![Snippet of the wix code page](https://github.com/anishhdiwan/wix_mathjax_tutorial/blob/main/Screenshot_wix.PNG)

- Now you can directly start writing code using the [asciimath](http://asciimath.org/) syntax. Make sure to wrap your (inline or regular) equations within the backtick (\`) delimiter. Example \` sum_(i=1)^n i^3=((n(n+1))/2)^2 \`



```
    <script>
        window.MathJax = {
            loader: { load: [ 'input/asciimath', 'ui/lazy', 'output/chtml', 'ui/menu'] }
        };

        (function() {
            var script = document.createElement('script');
            script.src = "https://cdn.jsdelivr.net/npm/mathjax@3/es5/startup.js";
            script.async = true;
            document.head.appendChild(script);
        })();
    </script>
```


```
    <!-- UpdateTypeset Script -->
    <script>
        var config = { attributes: true, childList: true, subtree: true };

        // Callback function to execute when mutations are observed
        var callback = (mutationList, observer) => {
            for (var mutation of mutationList) {
                if (mutation.type === 'childList') {
                    console.log('A child node has been added or removed.');
                    MathJax.typeset();
                } else if (mutation.type === 'attributes') {
                    console.log(`The ${mutation.attributeName} attribute was modified.`);
                }
            }
        };

        // Create an observer instance linked to the callback function
        var observer = new MutationObserver(callback);

        document.onreadystatechange = () => {
            if (document.readyState === 'complete') {
                console.log("Loaded fully according to readyState");
                var targetNode = document.getElementById('content-wrapper');
                console.log(targetNode);
                // Start observing the target node for configured mutations
                observer.observe(targetNode, config);
            }
        };
    </script>
```
