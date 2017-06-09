github: [terrencewei/ResponsiveMultiLevelMenu](https://github.com/terrencewei/ResponsiveMultiLevelMenu)

fork from [codrops/ResponsiveMultiLevelMenu](https://github.com/codrops/ResponsiveMultiLevelMenu)

my custom code for ResponsiveMultiLevelMenu:

* base html structure:
```
<div id="dl-menu" class="dl-menuwrapper">
   <button class="dl-trigger">Open Menu</button>
   <ul class="dl-menu">
        <li>
           <a href="#"><span>level 1</span><i class="fa"></i></a>
           <ul class="dl-submenu">
               <li class="dl-back">
                    <a href="#"><i class="fa"></i><span>go back</span></a>
               </li>
               <li>
                   <a href="#">level 2</a>
                   <ul class="dl-submenu">
                       <li class="dl-back">
                            <a href="#"><i class="fa"></i><span>go back</span></a>
                       </li>
                       <li>
                           <a href="#">level 3</a>
                       </li>
                   </ul>
               </li>
           </ul>
        </li>
   </ul>
</div>
```

* ResponsiveMultiLevelMenu/css/component.css

update the font path, from `../fonts` to `/fonts`
```
@font-face {
	font-family: 'icomoon';
	src:url('/fonts/icomoon.eot');
	src:url('/fonts/icomoon.eot?#iefix') format('embedded-opentype'),
		url('/fonts/icomoon.woff') format('woff'),
		url('/fonts/icomoon.ttf') format('truetype'),
		url('/fonts/icomoon.svg#icomoon') format('svg');
	font-weight: normal;
	font-style: normal;
}
```

create new animation class in out: dl-animate-in-custom dl-animate-out-custom
```
/* custom animationClasses in and out: dl-animate-in-custom dl-animate-out-custom start */
/* class */
.dl-menu.dl-animate-in-custom {
	-webkit-animation: MenuAnimInCustom 0.4s ease-in;
	animation: MenuAnimInCustom 0.4s ease-in;
}
.dl-menuwrapper > .dl-submenu.dl-animate-in-custom {
	-webkit-animation: SubMenuAnimInCustom 0.4s ease-in;
	animation: SubMenuAnimInCustom 0.4s ease-in;
}
.dl-menu.dl-animate-out-custom {
	-webkit-animation: MenuAnimOutCustom 0.4s ease-in;
	animation: MenuAnimOutCustom 0.4s ease-in;
}
.dl-menuwrapper > .dl-submenu.dl-animate-out-custom {
	-webkit-animation: SubMenuAnimOutCustom 0.4s ease-in;
	animation: SubMenuAnimOutCustom 0.4s ease-in;
}
/* keyframes */
@keyframes MenuAnimInCustom {
	0% {
		-webkit-transform: translateX(-100%);
		transform: translateX(-100%);
		opacity: 1;
	}
	100% {
		-webkit-transform: translateX(0px);
		transform: translateX(0px);
		opacity: 1;
	}
}
@keyframes SubMenuAnimInCustom {
	0% {
		-webkit-transform: translateX(100%);
		transform: translateX(100%);
		opacity: 1;
	}
	100% {
		-webkit-transform: translateX(0px);
		transform: translateX(0px);
		opacity: 1;
	}
}
@keyframes MenuAnimOutCustom {
	0% { }
	100% {
		-webkit-transform: translateX(-100%);
		transform: translateX(-100%);
		opacity: 1;
	}
}
@keyframes SubMenuAnimOutCustom {
	0% {
		-webkit-transform: translateX(0%);
		transform: translateX(0%);
		opacity: 1;
	}
	100% {
		-webkit-transform: translateX(100%);
		transform: translateX(100%);
		opacity: 1;
	}
}
/* -webkit-keyframes */
@-webkit-keyframes MenuAnimInCustom {
	0% {
		-webkit-transform: translateX(-100%);
		opacity: 1;
	}
	100% {
		-webkit-transform: translateX(0px);
		opacity: 1;
	}
}
@-webkit-keyframes SubMenuAnimInCustom {
	0% {
		-webkit-transform: translateX(100%);
		opacity: 1;
	}
	100% {
		-webkit-transform: translateX(0px);
		opacity: 1;
	}
}
@-webkit-keyframes MenuAnimOutCustom {
	0% { }
	100% {
		-webkit-transform: translateX(-100%);
		opacity: 1;
	}
}
@-webkit-keyframes SubMenuAnimOutCustom {
	0% {
		-webkit-transform: translateX(0%);
		opacity: 1;
	}
	100% {
		-webkit-transform: translateX(100%);
		opacity: 1;
	}
}
/* custom animationClasses in and out: dl-animate-in-custom dl-animate-out-custom end */
```
* ResponsiveMultiLevelMenu/js/jquery.dlmenu.js

add new options:
```
$.DLMenu.defaults = {
    ...
    // user click go back link, redirect instead go to back menu
    gobackRedirect: false,
    // user click menu title, redirect instead go to sub menu
    menuRedirect: true,
    // user click menu empty area is the same as click menu text
    clickMenuEmptyAreaAsText: false
    ...
}
```
add new functions to support new options:
```
$.DLMenu.prototype = {
    ...
    _checkRedirect: function (isRedirect, event) {
    
    var self = this;
    
    if (isRedirect) {
        if (!self.options.clickMenuEmptyAreaAsText) {
            // if clickMenuEmptyAreaAsText is false, need check if user click the menu text or menu empty area
            if ($(event.target).is("span")) {
                // if event.target is <span>, means user click the menu text
                isRedirect = true;
            } else {
                // if event.target is not <span>, means user click the menu empty area, not the menu text
                isRedirect = false;
            }
        }
    }
    if (isRedirect) {
        if ($(event.target).attr("href") && ($(event.target).attr("href") == '#' || $(event.target).attr("href") == 'javascript:void(0)')) {
            isRedirect = false;
        }
    }
    return isRedirect;
    },
    _isRedirect : function (event) {
    
    var self = this;
    
    var isRedirect = true;
    if (!self.options.menuRedirect || (self.options.menuRedirect && $(event.target).is("i"))) {
        // if menuRedirect is false, means click menu do not redirect
        // if menuRedirect is true but user click the <i> tag arrow icon, do not redirect
        isRedirect = false;
    }
    return self._checkRedirect(isRedirect, event);
    },
    _isGobackRedirect : function (event) {
    
    var self = this;
    
    var isRedirect = true;
    if (!self.options.gobackRedirect) {
        isRedirect = false;
    }
    return self._checkRedirect(isRedirect, event);
    },
    ...
}
```
add new check logic when user click sub menu and go back menu with new functions:
```
this.$menuitems.on( 'click.dlmenu', function( event ) {
    ...
    if (self._isRedirect(event)) {
        return;
    }
    ...
}):



this.$back.on( 'click.dlmenu', function( event ) {
    ...
    if (self._isGobackRedirect(event)) {
        return;
    }
    ...
}):
```