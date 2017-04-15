
#### Angular 2 code sample
This bug is one I worked through the other day in Angular 2. Unfortunately I can't show the entire code base, but I will lay out the problem here.
This app creates websites with dynamic content. Part of the structure is hard coded into Angular, 
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
