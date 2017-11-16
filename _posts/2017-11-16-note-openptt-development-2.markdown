---
layout: post
title:  "Note on OpenPTT Development 2"
date:   2017-11-16 13:00:00 +0800
categories: openptt android nodejs cordova
---

### Prerequisite

The previous note is [here](https://brli.github.io/2017/07/10/note-openptt-development.html).

This is a side note on deploying environment for Angular2+OnsenUI+Cordova project.

The upstream GitHub repo [url](https://github.com/OpenPTT/OpenPTT.git).

### Workaround testing development

1. Add browser platform to cordova

    ```bash
    (project_home)$ cordova platform add browser
    ---
    Using cordova-fetch for cordova-browser@~5.0.0
    Adding browser project...
    Creating Cordova project for cordova-browser:
        Path: /home/user/project/OpenPTT/platforms/browser
        Name: OpenPTT
    Installing "cordova-plugin-console" for browser
    Installing "cordova-plugin-whitelist" for browser
    Building Angular 2 application into "./www" directory.
    Hash: 943e4c83616aa1aff891                                                              
    Time: 26370ms
    chunk    {0} polyfills.ec7f97a1031757184077.bundle.js (polyfills) 266 kB {5} [initial] [rendered]
    chunk    {1} main.846fe0bd2b9e8cc40642.bundle.js (main) 5.82 kB {4} [initial] [rendered]
    chunk    {2} scripts.6c19cc76069a530d8626.bundle.js (scripts) 3.27 MB {5} [initial] [rendered]                                                                                                                                               
    chunk    {3} styles.ec0d4a6343e08003d065.bundle.css (styles) 175 bytes {5} [initial] [rendered]                                                                                                                                              
    chunk    {4} vendor.63d2b1ea5bb99dee0238.bundle.js (vendor) 5.76 MB [initial] [rendered]                                                                                                                                                     
    chunk    {5} inline.67d8d40982d64289248e.bundle.js (inline) 0 bytes [entry] [rendered]                                                                                                                                                       
                                                                                                                                                                                                                                                
    html file saved                                                                                                                                                                                                                              
    --save flag or autosave detected                                                                                                                                                                                                             
    Saving browser@~5.0.1 into config.xml file ...
    ```

2. Run the platform

    ```bash
    $ cordova run browser
    ---
    Building Angular 2 application into "./www" directory.                                                                                                                                                                                       
    Hash: 943e4c83616aa1aff891                                                                                                                                                                                                                   
    Time: 25876ms                                                                                                                                                                                                                                
    chunk    {0} polyfills.ec7f97a1031757184077.bundle.js (polyfills) 266 kB {5} [initial] [rendered]                                                                                                                                            
    chunk    {1} main.846fe0bd2b9e8cc40642.bundle.js (main) 5.82 kB {4} [initial] [rendered]                                                                                                                                                     
    chunk    {2} scripts.6c19cc76069a530d8626.bundle.js (scripts) 3.27 MB {5} [initial] [rendered]                                                                                                                                               
    chunk    {3} styles.ec0d4a6343e08003d065.bundle.css (styles) 175 bytes {5} [initial] [rendered]                                                                                                                                              
    chunk    {4} vendor.63d2b1ea5bb99dee0238.bundle.js (vendor) 5.76 MB [initial] [rendered]                                                                                                                                                     
    chunk    {5} inline.67d8d40982d64289248e.bundle.js (inline) 0 bytes [entry] [rendered]                                                                                                                                                       
                                                                                                                                                                                                                                                
    html file saved
    startPage = index.html
    Static file server running @ http://localhost:8000/index.html
    (...)
    ```

3. Open your browser and navigate to the url listed above

4. Check to use certain platform

    > add ?platform=android to the end of the URL

5. Use screen emulator to test the layout on phone
