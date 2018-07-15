<style>
#app {
  font-family: 'Avenir', Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  margin-top: 250px;
}
</style>

<template>
  <div id="app">
    <div class="tip">
      <h1>GiphyTooltip demo</h1>
      <h4 style="margin-top:40px;">Just select text and get GIFs!</h4>
      <span style="margin-top:10px;">Cats 😺, dogs 🐶 and unicorns 🦄!</span>
    </div>

    <div>
      <div id="template" style="display: none">
        Loading tooltip...
      </div>
    </div>

  </div>
</template>

<script>
// Importing giphy as a node module
const giphy = require('giphy-api')('Vthyya12NJQxIgW1BzmhDqIPv4GkTIEf');

export default {
  data () {
    return {
      
    }
  },

  mounted() {
    const template = document.querySelector('#template');
    const initialText = template.textContent;
    
    let that = this;

    const tip = tippy('.tip', {
      animation: 'shift-toward',
      arrow: true,
      html: '#template',
      trigger: 'click',
      onShow() {
        
        // `this` inside callbacks refers to the popper element
        const content = this.querySelector('.tippy-content');
        
        if(tip.loading || content.innerHTML !== initialText) 
          return;
        
        tip.loading = true;

        var self = that;
        
        $('#app').mouseup(function() {
          let selection = self.getSelected();
          
          if (selection != "") {

            giphy.translate(`${selection}`)
            .then(function (response) {
              // Assigning the url from response object to the url
              const url = response.data.images.fixed_width_small.url;
              content.innerHTML = `<img width="100" height="100" src="${url}">`;
              tip.loading = false;
            })
            .catch(function(error){
              content.innerHTML = 'Loading failed';
              tip.loading = false;
            });

          }
        });
      },

      onHidden() {
        const content = this.querySelector('.tippy-content');
        content.innerHTML = initialText;
      }
    })

  },

  methods: {
    // Function to get the selected text
    getSelected() {
      let selectedText = "", selection;
      
      if (window.getSelection) {
        selectedText = "" + window.getSelection();
      } 
      else if ( (selection = document.selection) && selection.type == "Text") {
        selectedText = selection.createRange().text;
      }
      return selectedText;
    }

  }
}
</script>