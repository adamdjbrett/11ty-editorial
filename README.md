# 11ty-editorial
v2 attempt at porting HTML5UP to 11ty

Editorial 11ty Feat Full Bootstrap Version Develop by Adam Dj Brett

### Need Help or Have Project ?? Contact Me
+ adamdjbrett.com
+ info@adamdjbrett.com

### How to use Multi Author

+ Register author on `_data/authors.yaml`
    - key is a url for your author profile page `key: john_dhoe`
+ Next you need to create author profile page on `content/authors/*.md`
    - example `jhon_dhoe.md` same with your author key on yaml data.
+ Insert in to post on author use author key from yaml data `_data/authors.yaml`
    - example single author `author: john_dhoe`
    - example multi author `author: john_dhoe , jon_jones`

### Icon and buttons on page , post and authors

Implementaion : 
```
social: 
 - title: Jons Facebook
   icon: fab fa-facebook
   url: https://fb.com
 - title: Jons Instagram
   icon: fab fa-instagram
   url: https://instagram.com
 - title: Jons Youtube
   icon: fab fa-youtube
   url: https://youtube.com
button:
 - title: Send Jons Message
   url: "info@example.com"
 - title: Call Jons
   url: "info@example.com"
```