
#### Angular 2 code sample
This code sample is based off an Angular 2 challenge I was working through at work. Unfortunately I can't show the entire code base, but I will lay out the problem here.
#### Overview
This app dynamically creates websites. Parts like the header, footer, and section code structure are hard coded into the Angular project, with unique images and copy coming from a JSON sitting on S3. Depending on certain parameters the images and copy can vary from site to site. 

The problem I am trying to solve is I need to dynamically apply a CSS class of `image-1` or `image-2` depending on the size of the image. The image can come in two possible dimensions, `280 x 531 or 531 x 431`. A separate component makes the `GET` request to S3, and the image url is avalable in our project as the `globalDirective.modelURL` property.
#### Component File
```
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
    let images = document.querySelectorAll('.model-image');
    //when img is loaded call function that calculates its width and height
    //images.addEventListener('load', (e) => this.handleImageLoad(e));
    [].forEach.call(images, (item) => {
      item.addEventListener('load', (e) => this.handleImageLoad(e));
    })
  }
}

```
#### Template File
```
<img [ngClass]="adjustImage()" class="model-image" alt="Model image" src={{globalDirective.modelURL}} />
```
