# imgpile
A pile of images on your website.

If you have more images than pixels, you could let a group of related images share the same space on screen. The images form a 'pile' of images. You can see the image that is on top of the pile (the last one added in the html) rightaway. 

Surround the group of images with a link tag, and put some javascript on that.
Clicking the tag should then fadeout the top image, and move it to the bottom of the pile. And so after one click you will see the next image of the pile. And so on and so on.

So the html would be:

        <a href="#" class="imgpile">
                <img src="image_1.jpg" />
                <img src="image_2.jpg" />
                <img src="image_3.jpg" />
                <img src="image_4.jpg" />
        </a>

I found a [nice way for the fading out of the top image](http://www.chrisbuttery.com/articles/fade-in-fade-out-with-javascript/) on the internet, which i adapted slightly.

The javascript goes like this:

        // fadeout, remove, prepend and then fadein the last img child
        function imgpile(event)
        {
                event.preventDefault();
        
                var link = event.currentTarget;
                var images = link.querySelectorAll('img');
                var last = images[images.length - 1];
                var first = images[0];

                if(!(last.style.opacity)) last.style.opacity = 1;
                (function anim() {
                        if ((last.style.opacity -= .05) < 0) {
                                last.style.opacity = 0;
                                link.removeChild(last);
                                link.insertBefore(last, first);
                                last.style.opacity = 1;
                        } else {
                                requestAnimFrame(anim);
                        }
                })();
        }

As you can see, i use the requestAnimFrame() function. This function does not actually exist, but i define it so, that things will work in as many browsers as possible. Found on [this excellent blog](http://www.paulirish.com/2011/requestanimationframe-for-smart-animating/). 

        window.requestAnimFrame = (function(){
                return window.requestAnimFrame            ||
                       window.requestAnimationFrame       ||
                       window.webkitRequestAnimationFrame ||
                       window.mozRequestAnimationFrame    ||
                       function( callback ){
                               window.setTimeout(callback, 1000 / 60);
                       };
        })();

I modified the original slightly: if some other included JS file already defined window.requestAnimFrame function, then it will not get re-defined.

Of course, after loading the HTML of the page, all the links with CSS class "imgpile" should have their eventhandlers attached like so:

        // init: attach event handlers all around
        var links = document.querySelectorAll('a.imgpile');
                for(i = 0; i < links.length; i++)
                {
                        if(links[i].addEventListener){
                                links[i].addEventListener('click', imgpile);
                } else {
                        links[i].attachEvent('onclick', imgpile);
                }
        }

I included the attachEvent method so that IE8 users will be happy too.

The only thing that is missing is a scratch of CSS:

        .imgpile img { position: absolute; top: 0px; left: 0px; }
        .imgpile img:last-child { position: relative; }

The last child is positioned normally, and all other images are positioned absolute. This will establish that all images form a 'pile' with the last image on top. This image is positioned relative, and so the surrounding link will be sized according to the dimensions of this last image.

In my case, i create images all having the same size, but one could experiment with images of different sizes.

You could also spice things up with declaring a few px offset for the pixels inside the pile so that they will 'stick out' on various sides. This would be a nice hint to the user to see what happens if i click on this...

johan 2016-05-22
