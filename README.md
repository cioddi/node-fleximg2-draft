## Idea

For any image website on any website we need 4 pieces of information to uniquely determine the desired display size of an image.

#### what we know

- page url
- window width
- window height
- img src

#### what we want to know

- width
- height
- crop:
  -   x
  -   y
  -   width
  -   height

We can assign exactly on set of [width,height,crop_info] to each set of [page_url,window_width,window_height,img_src]. 

### how it works

- ```node refreshdb``` will crawl the Webpages at the given urls with different browser dimensions (200x400,250x400,300x400,...,200x450,300x450) and create a JSON object for every image and every dimension combinations on any page. All the data is written to a database. **example:**

```
{
      url:'/blog/2013/10/30',
      window_width:1000,
      window_height:600,
      src:'/img/gallery/plane.jpg',
      width:200,
      height:200,
      crop:{
        x:50,
        y:50,
        width:400,
        height:400,
      }
    }
```

- a script in the website head will collect information about window width and height and send that data as an xhr request to the server where it will be stored in a session variable (that session data needs to be updated on window resize)
- all image request go through a script which will use the db to lookup the correct image size based on page_url,and img_src from the request and window_width and window_height from the session and then deliver right image

### advantages

- no DOM rewriting
- only need to include one script no code change at all
- decide img width height and cropping just by applying css
- after the session is set it would work even well with Browsers lookahead asset parsers 

### disadvantages

- not suitable for websites where the content changes frequently every content change requires reindexing
- could require some flexible and performant hardware to be fast

## Requirements

- mongodb
- node.js

## Usage

- ```$ npm install node-fleximg-2```
- start mongodb ```$ mongod```
- edit index.js and insert url
- run ```node refreshdb```

### mongodb collection

```
{
  images:[
    {
      url:'/blog/2013/10/30',
      window_width:1000,
      window_height:600,
      src:'/img/gallery/plane.jpg',
      width:200,
      height:200,
      crop:{
        x:50,
        y:50,
        width:400,
        height:400,
      }
    },
    {
      url:'/blog/2013/10/30',
      window_width:500,
      window_height:200,
      src:'/img/gallery/plane.jpg',
      width:100,
      height:100,
      crop:{
        x:50,
        y:50,
        width:400,
        height:400,
      }
    }
  ]
}
```