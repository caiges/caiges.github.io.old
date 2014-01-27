---
layout: post
title:  "Rails Asset Pipeline - Managing dependencies"
date:   2013-11-01 20:10:16
categories: rails asset-pipline
---

After a long week working with Rails in which we use JavaScript extensively I picked up a pattern that had eluded me until today. The premise is "How do you manage dependencies within the pipeline without relying on careful ordering of scripts in the 'application.js' manifest?"

## The Problem

The solution to this problem is described in [Rails' Asset Pipeline documentation](http://guides.rubyonrails.org/asset_pipeline.html#manifest-files-and-directives):

> Directives are processed top to bottom, but the order in which files are included by require_tree is unspecified. You should not rely on any particular order among those. If you need to ensure some particular JavaScript ends up above some other in the concatenated file, require the prerequisite file first in the manifest. Note that the family of require directives prevents files from being included twice in the output.

This means if you need "taco.js" before "shrimp.js", require it in the manifest before the "require_tree" directive:

Assume we have a file structure like this:

    app/assets/javascripts/application.js
        app/assets/javascripts/taco.js
        app/assets/javascripts/shrimp.js

*app/assets/javascripts/application.js*

    //= require taco
    //= require_tree .

While this approach works, it can get out of hand very quickly and overall, doesn't feel right.

## The Solution

A not so obvious yet simple solution to keeping things tidy is to merely move the dependency to the file that actually needs it.

*app/assets/javascripts/shrimp.js*

    //= require taco
    
    ... make the sauce
    
*app/assets/javascripts/application.js*

    //= require shrimp
    
This ensures that *taco.js* will be loaded before *shrimp.js* and keeps the application manifest, *application.js* nice and clean.