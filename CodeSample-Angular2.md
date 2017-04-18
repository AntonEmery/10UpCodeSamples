
#### Angular 2 code sample
This code sample is based off an Angular 2 bug I was working through at work. Unfortunately I can't show the entire code base, but I will lay out the problem and my solution here.
#### Overview
This app dynamically creates template based websites. Parts like the header, footer, and section code structure are hard coded into the Angular project, with unique images and copy coming from a JSON sitting on S3. Depending on certain parameters the images and copy can vary from site to site. 

The problem I am trying to solve is I need to dynamically apply a CSS class of `image-1` or `image-2` depending on the size of the image. The image can come in two possible dimensions, `280 x 531 or 531 x 431`. A separate component makes the `GET` request to S3, and the image url is avalable in our project as the `globalDirective.modelURL` property.

Lets take a look at the initial implementation. 
#### Component File
```javascript
import { Component, OnInit } from '@angular/core';
import { GlobalDirective } from '../../global.directive';

@Component({
  selector: 'model-image',
  templateUrl: './model.component.html',
  styleUrls: ['./model.component.css']
})
export class ModelComponent implements OnInit {

  width: number;
  height: number;

  constructor(public globalDirective: GlobalDirective) { }

  adjustImage() {
    //two possible widths and heights are 280 x 531 OR 531 x 431
    //return value is assigned as a class to the image in template
    if(this.width == 280 && this.height == 531) {
      return "image-1";
    } else if(this.width == 531 && this.height == 431) {
      return "image-2";
    }
  }

  handleImageLoad(e): void {
    console.log(e.target.width + 'and' + e.target.height)
    //assign image's width and height to local variables
    this.width = e.target.width;
    this.height = e.target.height;
  }

  ngOnInit() {
    //select image on template with id of model
    let image = document.querySelector('.model-image');
    //when img is loaded call function that calculates its width and height
    image.addEventListener('load', (e) => this.handleImageLoad(e));
  }

```
#### Template File
```html
<img [ngClass]="adjustImage()" class="model-image" alt="Model image" src={{globalDirective.modelURL}} />
```
`ngOnInit()` is called after the directive's data bound properties have been checked for the first time. `document.querySelector` selects the element with the class `.model-image` We assign an event listener that calls `handleImageLoad()` after the image has loaded and checks the heigth and width, assigning them to variables in our controller. 

In the template `[ngClass]` allows us to dynamically assign a class to the image, and calls `adjustImage()` That function checks the variables in the controller and assigns the image the class of `image-1` or `image-2`

This solution worked fine initially when there was only one image on the page, but before long I had to do the same thing to multiple images. Initially i was not sure how to proceed, but in the end here is what worked for me.

```Javascript
ngOnInit() {
    //select image on template with id of model
    let images = document.querySelectorAll('.model-image');
    //when img is loaded call function that calculates its width and height
    Array.prototype.forEach.call(images, (item) => {
      item.addEventListener('load', (e) => this.handleImageLoad(e));
    })
  }
}
```
`document.querySelectorAll` returns a node list of all the elements that match, whereas `document.querySelector` only returns the first match. I thought I could just use `forEach` to iterate over the nodelist but it does not have that method. 

After a bit of research I learned I could use `.call` to use the `forEach` method belonging to type `Array` and apply to my `node list` since they both contain the property `length`. In `.call` I pass in the object I want to use, in this case the `images` variable, and then the action I want to take when `forEach` iterates over each item. I use the same eventlistener as previous, and each image with the class `.model-image` is evaluated and the correct class dynamically applied.
