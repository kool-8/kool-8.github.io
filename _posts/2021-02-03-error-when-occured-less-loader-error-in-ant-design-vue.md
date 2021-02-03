---
layout: post
tags:
- vue
- error
- antdesign
categories: ''
title: "[Error] when occured less loader error in ant design Vue"

---
    // vue.config.js for less-loader@6.0.0
    module.exports = {
      css: {
        loaderOptions: {
          less: {
            lessOptions: {
              modifyVars: {
                'primary-color': '#1DA57A',
                'link-color': '#1DA57A',
                'border-radius-base': '2px',
              },
              javascriptEnabled: true,
            },
          },
        },
      },
    };

must check less-loader version.

antdesign vue lessloader version is @6.0.0!