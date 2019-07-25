webpackJsonp([0],{

/***/ "./node_modules/tilt.js/src/tilt.jquery.js":
/***/ (function(module, exports, __webpack_require__) {

var __WEBPACK_AMD_DEFINE_FACTORY__, __WEBPACK_AMD_DEFINE_ARRAY__, __WEBPACK_AMD_DEFINE_RESULT__;(function (factory) {
    if (true) {
        // AMD. Register as an anonymous module.
        !(__WEBPACK_AMD_DEFINE_ARRAY__ = [__webpack_require__("./node_modules/jquery/dist/jquery.js")], __WEBPACK_AMD_DEFINE_FACTORY__ = (factory),
				__WEBPACK_AMD_DEFINE_RESULT__ = (typeof __WEBPACK_AMD_DEFINE_FACTORY__ === 'function' ?
				(__WEBPACK_AMD_DEFINE_FACTORY__.apply(exports, __WEBPACK_AMD_DEFINE_ARRAY__)) : __WEBPACK_AMD_DEFINE_FACTORY__),
				__WEBPACK_AMD_DEFINE_RESULT__ !== undefined && (module.exports = __WEBPACK_AMD_DEFINE_RESULT__));
    } else if (typeof module === 'object' && module.exports) {
        // Node/CommonJS
        module.exports = function( root, jQuery ) {
            if ( jQuery === undefined ) {
                // require('jQuery') returns a factory that requires window to
                // build a jQuery instance, we normalize how we use modules
                // that require this pattern but the window provided is a noop
                // if it's defined (how jquery works)
                if ( typeof window !== 'undefined' ) {
                    jQuery = require('jquery');
                }
                else {
                    jQuery = require('jquery')(root);
                }
            }
            factory(jQuery);
            return jQuery;
        };
    } else {
        // Browser globals
        factory(jQuery);
    }
}(function ($) {
    $.fn.tilt = function (options) {

        /**
         * RequestAnimationFrame
         */
        const requestTick = function() {
            if (this.ticking) return;
            requestAnimationFrame(updateTransforms.bind(this));
            this.ticking = true;
        };

        /**
         * Bind mouse movement evens on instance
         */
        const bindEvents = function() {
            const _this = this;
            $(this).on('mousemove', mouseMove);
            $(this).on('mouseenter', mouseEnter);
            if (this.settings.reset) $(this).on('mouseleave', mouseLeave);
            if (this.settings.glare) $(window).on('resize', updateGlareSize.bind(_this));
        };

        /**
         * Set transition only on mouse leave and mouse enter so it doesn't influence mouse move transforms
         */
        const setTransition = function() {
            if (this.timeout !== undefined) clearTimeout(this.timeout);
            $(this).css({'transition': `${this.settings.speed}ms ${this.settings.easing}`});
            if(this.settings.glare) this.glareElement.css({'transition': `opacity ${this.settings.speed}ms ${this.settings.easing}`});
            this.timeout = setTimeout(() => {
                $(this).css({'transition': ''});
                if(this.settings.glare) this.glareElement.css({'transition': ''});
            }, this.settings.speed);
        };

        /**
         * When user mouse enters tilt element
         */
        const mouseEnter = function(event) {
            this.ticking = false;
            $(this).css({'will-change': 'transform'});
            setTransition.call(this);

            // Trigger change event
            $(this).trigger("tilt.mouseEnter");
        };

        /**
         * Return the x,y position of the mouse on the tilt element
         * @returns {{x: *, y: *}}
         */
        const getMousePositions = function(event) {
            if (typeof(event) === "undefined") {
                event = {
                    pageX: $(this).offset().left + $(this).outerWidth() / 2,
                    pageY: $(this).offset().top + $(this).outerHeight() / 2
                };
            }
            return {x: event.pageX, y: event.pageY};
        };

        /**
         * When user mouse moves over the tilt element
         */
        const mouseMove = function(event) {
            this.mousePositions = getMousePositions(event);
            requestTick.call(this);
        };

        /**
         * When user mouse leaves tilt element
         */
        const mouseLeave = function() {
            setTransition.call(this);
            this.reset = true;
            requestTick.call(this);

            // Trigger change event
            $(this).trigger("tilt.mouseLeave");
        };

        /**
         * Get tilt values
         *
         * @returns {{x: tilt value, y: tilt value}}
         */
        const getValues = function() {
            const width = $(this).outerWidth();
            const height = $(this).outerHeight();
            const left = $(this).offset().left;
            const top = $(this).offset().top;
            const percentageX = (this.mousePositions.x - left) / width;
            const percentageY = (this.mousePositions.y - top) / height;
            // x or y position inside instance / width of instance = percentage of position inside instance * the max tilt value
            const tiltX = ((this.settings.maxTilt / 2) - ((percentageX) * this.settings.maxTilt)).toFixed(2);
            const tiltY = (((percentageY) * this.settings.maxTilt) - (this.settings.maxTilt / 2)).toFixed(2);
            // angle
            const angle = Math.atan2(this.mousePositions.x - (left+width/2),- (this.mousePositions.y - (top+height/2)) )*(180/Math.PI);
            // Return x & y tilt values
            return {tiltX, tiltY, 'percentageX': percentageX * 100, 'percentageY': percentageY * 100, angle};
        };

        /**
         * Update tilt transforms on mousemove
         */
        const updateTransforms = function() {
            this.transforms = getValues.call(this);

            if (this.reset) {
                this.reset = false;
                $(this).css('transform', `perspective(${this.settings.perspective}px) rotateX(0deg) rotateY(0deg)`);

                // Rotate glare if enabled
                if (this.settings.glare){
                    this.glareElement.css('transform', `rotate(180deg) translate(-50%, -50%)`);
                    this.glareElement.css('opacity', `0`);
                }

                return;
            } else {
                $(this).css('transform', `perspective(${this.settings.perspective}px) rotateX(${this.settings.disableAxis === 'x' ? 0 : this.transforms.tiltY}deg) rotateY(${this.settings.disableAxis === 'y' ? 0 : this.transforms.tiltX}deg) scale3d(${this.settings.scale},${this.settings.scale},${this.settings.scale})`);

                // Rotate glare if enabled
                if (this.settings.glare){
                    this.glareElement.css('transform', `rotate(${this.transforms.angle}deg) translate(-50%, -50%)`);
                    this.glareElement.css('opacity', `${this.transforms.percentageY * this.settings.maxGlare / 100}`);
                }
            }

            // Trigger change event
            $(this).trigger("change", [this.transforms]);

            this.ticking = false;
        };

        /**
         * Prepare elements
         */
        const prepareGlare = function () {
            const glarePrerender = this.settings.glarePrerender;

            // If option pre-render is enabled we assume all html/css is present for an optimal glare effect.
            if (!glarePrerender)
            // Create glare element
                $(this).append('<div class="js-tilt-glare"><div class="js-tilt-glare-inner"></div></div>');

            // Store glare selector if glare is enabled
            this.glareElementWrapper = $(this).find(".js-tilt-glare");
            this.glareElement = $(this).find(".js-tilt-glare-inner");

            // Remember? We assume all css is already set, so just return
            if (glarePrerender) return;

            // Abstracted re-usable glare styles
            const stretch = {
                'position': 'absolute',
                'top': '0',
                'left': '0',
                'width': '100%',
                'height': '100%',
            };

            // Style glare wrapper
            this.glareElementWrapper.css(stretch).css({
                'overflow': 'hidden',
                'pointer-events': 'none',
            });

            // Style glare element
            this.glareElement.css({
                'position': 'absolute',
                'top': '50%',
                'left': '50%',
                'background-image': `linear-gradient(0deg, rgba(255,255,255,0) 0%, rgba(255,255,255,1) 100%)`,
                'width': `${$(this).outerWidth()*2}`,
                'height': `${$(this).outerWidth()*2}`,
                'transform': 'rotate(180deg) translate(-50%, -50%)',
                'transform-origin': '0% 0%',
                'opacity': '0',
            });

        };

        /**
         * Update glare on resize
         */
        const updateGlareSize = function () {
            this.glareElement.css({
                'width': `${$(this).outerWidth()*2}`,
                'height': `${$(this).outerWidth()*2}`,
            });
        };

        /**
         * Public methods
         */
        $.fn.tilt.destroy = function() {
            $(this).each(function () {
                $(this).find('.js-tilt-glare').remove();
                $(this).css({'will-change': '', 'transform': ''});
                $(this).off('mousemove mouseenter mouseleave');
            });
        };

        $.fn.tilt.getValues = function() {
            const results = [];
            $(this).each(function () {
                this.mousePositions = getMousePositions.call(this);
                results.push(getValues.call(this));
            });
            return results;
        };

        $.fn.tilt.reset = function() {
            $(this).each(function () {
                this.mousePositions = getMousePositions.call(this);
                this.settings = $(this).data('settings');
                mouseLeave.call(this);
                setTimeout(() => {
                    this.reset = false;
                }, this.settings.transition);
            });
        };

        /**
         * Loop every instance
         */
        return this.each(function () {

            /**
             * Default settings merged with user settings
             * Can be set trough data attributes or as parameter.
             * @type {*}
             */
            this.settings = $.extend({
                maxTilt: $(this).is('[data-tilt-max]') ? $(this).data('tilt-max') : 20,
                perspective: $(this).is('[data-tilt-perspective]') ? $(this).data('tilt-perspective') : 300,
                easing: $(this).is('[data-tilt-easing]') ? $(this).data('tilt-easing') : 'cubic-bezier(.03,.98,.52,.99)',
                scale: $(this).is('[data-tilt-scale]') ? $(this).data('tilt-scale') : '1',
                speed: $(this).is('[data-tilt-speed]') ? $(this).data('tilt-speed') : '400',
                transition: $(this).is('[data-tilt-transition]') ? $(this).data('tilt-transition') : true,
                disableAxis: $(this).is('[data-tilt-disable-axis]') ? $(this).data('tilt-disable-axis') : null,
                axis: $(this).is('[data-tilt-axis]') ? $(this).data('tilt-axis') : null,
                reset: $(this).is('[data-tilt-reset]') ? $(this).data('tilt-reset') : true,
                glare: $(this).is('[data-tilt-glare]') ? $(this).data('tilt-glare') : false,
                maxGlare: $(this).is('[data-tilt-maxglare]') ? $(this).data('tilt-maxglare') : 1,
            }, options);

            // Add deprecation warning & set disableAxis to deprecated axis setting
            if(this.settings.axis !== null){
                console.warn('Tilt.js: the axis setting has been renamed to disableAxis. See https://github.com/gijsroge/tilt.js/pull/26 for more information');
                this.settings.disableAxis = this.settings.axis;
            }

            this.init = () => {
                // Store settings
                $(this).data('settings', this.settings);

                // Prepare element
                if(this.settings.glare) prepareGlare.call(this);

                // Bind events
                bindEvents.call(this);
            };

            // Init
            this.init();

        });
    };

    /**
     * Auto load
     */
    $('[data-tilt]').tilt();

    return true;
}));

/***/ }),

/***/ "./src/js/app.js":
/***/ (function(module, __webpack_exports__, __webpack_require__) {

"use strict";
Object.defineProperty(__webpack_exports__, "__esModule", { value: true });
/* WEBPACK VAR INJECTION */(function($) {/* harmony import */ var __WEBPACK_IMPORTED_MODULE_0_tilt_js__ = __webpack_require__("./node_modules/tilt.js/src/tilt.jquery.js");
/* harmony import */ var __WEBPACK_IMPORTED_MODULE_0_tilt_js___default = __webpack_require__.n(__WEBPACK_IMPORTED_MODULE_0_tilt_js__);
function _defineProperty(obj, key, value) { if (key in obj) { Object.defineProperty(obj, key, { value: value, enumerable: true, configurable: true, writable: true }); } else { obj[key] = value; } return obj; }



$('.modal-toggle').on('click', function (e) {
  e.preventDefault();
  $('body').toggleClass('overflow-hidden');
  $('.modal').toggleClass('is-visible');
});

$(document).ready(function () {
  var tilt = $('.js-tilt').tilt({
    scale: 1.2,
    glare: true,
    maxGlare: .3
  });

  // Color Picker Tool Js
  var themeSwitchers = document.querySelectorAll('.default-switch');
  var dynamicInputs = document.querySelectorAll('input.input-color-picker');

  var handleThemeUpdate = function handleThemeUpdate(cssVars) {
    var root = document.querySelector(':root');
    var keys = Object.keys(cssVars);
    keys.forEach(function (key) {
      root.style.setProperty(key, cssVars[key]);
      $.fn.changeColorForm(key, cssVars[key]);
    });
  };

  themeSwitchers.forEach(function (item) {
    item.addEventListener('click', function (e) {
      var bgColor = e.target.getAttribute('data-bg-color');
      var color = e.target.getAttribute('data-color');
      handleThemeUpdate({
        '--primary-bg-color': bgColor,
        '--primary-color': color
      });
      $("input.input-color-picker[data-id='color']").val(color);
      $("input.input-color-picker[data-id='bg-color']").val(bgColor);
    });
  });

  dynamicInputs.forEach(function (item) {
    item.addEventListener('input', function (e) {
      var cssPropName = '--primary-' + e.target.getAttribute('data-id');
      handleThemeUpdate(_defineProperty({}, cssPropName, e.target.value));
    });
  });

  $.fn.changeColorForm = function (key, color) {
    $('form[name=contact] input[name=\'' + key + '\']').val(color);
  };
});
/* WEBPACK VAR INJECTION */}.call(__webpack_exports__, __webpack_require__("./node_modules/jquery/dist/jquery.js")))

/***/ }),

/***/ "./src/sass/app.scss":
/***/ (function(module, exports) {

// removed by extract-text-webpack-plugin

/***/ }),

/***/ "./src/sass/tailwind.css":
/***/ (function(module, exports) {

// removed by extract-text-webpack-plugin

/***/ }),

/***/ 0:
/***/ (function(module, exports, __webpack_require__) {

__webpack_require__("./src/js/app.js");
__webpack_require__("./src/sass/app.scss");
module.exports = __webpack_require__("./src/sass/tailwind.css");


/***/ })

},[0]);
//# sourceMappingURL=data:application/json;charset=utf-8;base64,eyJ2ZXJzaW9uIjozLCJzb3VyY2VzIjpbIndlYnBhY2s6Ly8vLi9ub2RlX21vZHVsZXMvdGlsdC5qcy9zcmMvdGlsdC5qcXVlcnkuanMiLCJ3ZWJwYWNrOi8vLy4vc3JjL2pzL2FwcC5qcyIsIndlYnBhY2s6Ly8vLi9zcmMvc2Fzcy9hcHAuc2Nzcz9kOTVhIiwid2VicGFjazovLy8uL3NyYy9zYXNzL3RhaWx3aW5kLmNzcz8yMDgyIl0sIm5hbWVzIjpbIiQiLCJvbiIsImUiLCJwcmV2ZW50RGVmYXVsdCIsInRvZ2dsZUNsYXNzIiwiZG9jdW1lbnQiLCJyZWFkeSIsInRpbHQiLCJzY2FsZSIsImdsYXJlIiwibWF4R2xhcmUiLCJ0aGVtZVN3aXRjaGVycyIsInF1ZXJ5U2VsZWN0b3JBbGwiLCJkeW5hbWljSW5wdXRzIiwiaGFuZGxlVGhlbWVVcGRhdGUiLCJjc3NWYXJzIiwicm9vdCIsInF1ZXJ5U2VsZWN0b3IiLCJrZXlzIiwiT2JqZWN0IiwiZm9yRWFjaCIsInN0eWxlIiwic2V0UHJvcGVydHkiLCJrZXkiLCJmbiIsImNoYW5nZUNvbG9yRm9ybSIsIml0ZW0iLCJhZGRFdmVudExpc3RlbmVyIiwiYmdDb2xvciIsInRhcmdldCIsImdldEF0dHJpYnV0ZSIsImNvbG9yIiwidmFsIiwiY3NzUHJvcE5hbWUiLCJ2YWx1ZSJdLCJtYXBwaW5ncyI6Ijs7Ozs7QUFBQTtBQUNBLFFBQVEsSUFBMEM7QUFDbEQ7QUFDQSxRQUFRLGlDQUFPLENBQUMsMkRBQVEsQ0FBQyxvQ0FBRSxPQUFPO0FBQUE7QUFBQTtBQUFBLG9HQUFDO0FBQ25DLEtBQUs7QUFDTDtBQUNBO0FBQ0E7QUFDQTtBQUNBO0FBQ0E7QUFDQTtBQUNBO0FBQ0E7QUFDQTtBQUNBO0FBQ0E7QUFDQTtBQUNBO0FBQ0E7QUFDQTtBQUNBO0FBQ0EsS0FBSztBQUNMO0FBQ0E7QUFDQTtBQUNBLENBQUM7QUFDRDs7QUFFQTtBQUNBO0FBQ0E7QUFDQTtBQUNBO0FBQ0E7QUFDQTtBQUNBOztBQUVBO0FBQ0E7QUFDQTtBQUNBO0FBQ0E7QUFDQTtBQUNBO0FBQ0E7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LEtBVEQ7QUFVRCxHQVhEOztBQWFBZixnQkFBY08sT0FBZCxDQUFzQixVQUFDTSxJQUFELEVBQVU7QUFDOUJBLFNBQUtDLGdCQUFMLENBQXNCLE9BQXRCLEVBQStCLFVBQUN6QixDQUFELEVBQU87QUFDcEMsVUFBTStCLDZCQUEyQi9CLEVBQUUyQixNQUFGLENBQVNDLFlBQVQsQ0FBc0IsU0FBdEIsQ0FBakM7QUFDQWhCLDRDQUNHbUIsV0FESCxFQUNpQi9CLEVBQUUyQixNQUFGLENBQVNLLEtBRDFCO0FBR0QsS0FMRDtBQU1ELEdBUEQ7O0FBU0FsQyxJQUFFd0IsRUFBRixDQUFLQyxlQUFMLEdBQXVCLFVBQVVGLEdBQVYsRUFBZVEsS0FBZixFQUFzQjtBQUMzQy9CLDJDQUFvQ3VCLEdBQXBDLFVBQTZDUyxHQUE3QyxDQUFpREQsS0FBakQ7QUFDRCxHQUZEO0FBSUQsQ0E5Q0QsRTs7Ozs7Ozs7QUNSQSx5Qzs7Ozs7OztBQ0FBLHlDIiwiZmlsZSI6Ii9qcy9hcHAuanMiLCJzb3VyY2VzQ29udGVudCI6WyIoZnVuY3Rpb24gKGZhY3RvcnkpIHtcbiAgICBpZiAodHlwZW9mIGRlZmluZSA9PT0gJ2Z1bmN0aW9uJyAmJiBkZWZpbmUuYW1kKSB7XG4gICAgICAgIC8vIEFNRC4gUmVnaXN0ZXIgYXMgYW4gYW5vbnltb3VzIG1vZHVsZS5cbiAgICAgICAgZGVmaW5lKFsnanF1ZXJ5J10sIGZhY3RvcnkpO1xuICAgIH0gZWxzZSBpZiAodHlwZW9mIG1vZHVsZSA9PT0gJ29iamVjdCcgJiYgbW9kdWxlLmV4cG9ydHMpIHtcbiAgICAgICAgLy8gTm9kZS9Db21tb25KU1xuICAgICAgICBtb2R1bGUuZXhwb3J0cyA9IGZ1bmN0aW9uKCByb290LCBqUXVlcnkgKSB7XG4gICAgICAgICAgICBpZiAoIGpRdWVyeSA9PT0gdW5kZWZpbmVkICkge1xuICAgICAgICAgICAgICAgIC8vIHJlcXVpcmUoJ2pRdWVyeScpIHJldHVybnMgYSBmYWN0b3J5IHRoYXQgcmVxdWlyZXMgd2luZG93IHRvXG4gICAgICAgICAgICAgICAgLy8gYnVpbGQgYSBqUXVlcnkgaW5zdGFuY2UsIHdlIG5vcm1hbGl6ZSBob3cgd2UgdXNlIG1vZHVsZXNcbiAgICAgICAgICAgICAgICAvLyB0aGF0IHJlcXVpcmUgdGhpcyBwYXR0ZXJuIGJ1dCB0aGUgd2luZG93IHByb3ZpZGVkIGlzIGEgbm9vcFxuICAgICAgICAgICAgICAgIC8vIGlmIGl0J3MgZGVmaW5lZCAoaG93IGpxdWVyeSB3b3JrcylcbiAgICAgICAgICAgICAgICBpZiAoIHR5cGVvZiB3aW5kb3cgIT09ICd1bmRlZmluZWQnICkge1xuICAgICAgICAgICAgICAgICAgICBqUXVlcnkgPSByZXF1aXJlKCdqcXVlcnknKTtcbiAgICAgICAgICAgICAgICB9XG4gICAgICAgICAgICAgICAgZWxzZSB7XG4gICAgICAgICAgICAgICAgICAgIGpRdWVyeSA9IHJlcXVpcmUoJ2pxdWVyeScpKHJvb3QpO1xuICAgICAgICAgICAgICAgIH1cbiAgICAgICAgICAgIH1cbiAgICAgICAgICAgIGZhY3RvcnkoalF1ZXJ5KTtcbiAgICAgICAgICAgIHJldHVybiBqUXVlcnk7XG4gICAgICAgIH07XG4gICAgfSBlbHNlIHtcbiAgICAgICAgLy8gQnJvd3NlciBnbG9iYWxzXG4gICAgICAgIGZhY3RvcnkoalF1ZXJ5KTtcbiAgICB9XG59KGZ1bmN0aW9uICgkKSB7XG4gICAgJC5mbi50aWx0ID0gZnVuY3Rpb24gKG9wdGlvbnMpIHtcblxuICAgICAgICAvKipcbiAgICAgICAgICogUmVxdWVzdEFuaW1hdGlvbkZyYW1lXG4gICAgICAgICAqL1xuICAgICAgICBjb25zdCByZXF1ZXN0VGljayA9IGZ1bmN0aW9uKCkge1xuICAgICAgICAgICAgaWYgKHRoaXMudGlja2luZykgcmV0dXJuO1xuICAgICAgICAgICAgcmVxdWVzdEFuaW1hdGlvbkZyYW1lKHVwZGF0ZVRyYW5zZm9ybXMuYmluZCh0aGlzKSk7XG4gICAgICAgICAgICB0aGlzLnRpY2tpbmcgPSB0cnVlO1xuICAgICAgICB9O1xuXG4gICAgICAgIC8qKlxuICAgICAgICAgKiBCaW5kIG1vdXNlIG1vdmVtZW50IGV2ZW5zIG9uIGluc3RhbmNlXG4gICAgICAgICAqL1xuICAgICAgICBjb25zdCBiaW5kRXZlbnRzID0gZnVuY3Rpb24oKSB7XG4gICAgICAgICAgICBjb25zdCBfdGhpcyA9IHRoaXM7XG4gICAgICAgICAgICAkKHRoaXMpLm9uKCdtb3VzZW1vdmUnLCBtb3VzZU1vdmUpO1xuICAgICAgICAgICAgJCh0aGlzKS5vbignbW91c2VlbnRlcicsIG1vdXNlRW50ZXIpO1xuICAgICAgICAgICAgaWYgKHRoaXMuc2V0dGluZ3MucmVzZXQpICQodGhpcykub24oJ21vdXNlbGVhdmUnLCBtb3VzZUxlYXZlKTtcbiAgICAgICAgICAgIGlmICh0aGlzLnNldHRpbmdzLmdsYXJlKSAkKHdpbmRvdykub24oJ3Jlc2l6ZScsIHVwZGF0ZUdsYXJlU2l6ZS5iaW5kKF90aGlzKSk7XG4gICAgICAgIH07XG5cbiAgICAgICAgLyoqXG4gICAgICAgICAqIFNldCB0cmFuc2l0aW9uIG9ubHkgb24gbW91c2UgbGVhdmUgYW5kIG1vdXNlIGVudGVyIHNvIGl0IGRvZXNuJ3QgaW5mbHVlbmNlIG1vdXNlIG1vdmUgdHJhbnNmb3Jtc1xuICAgICAgICAgKi9cbiAgICAgICAgY29uc3Qgc2V0VHJhbnNpdGlvbiA9IGZ1bmN0aW9uKCkge1xuICAgICAgICAgICAgaWYgKHRoaXMudGltZW91dCAhPT0gdW5kZWZpbmVkKSBjbGVhclRpbWVvdXQodGhpcy50aW1lb3V0KTtcbiAgICAgICAgICAgICQodGhpcykuY3NzKHsndHJhbnNpdGlvbic6IGAke3RoaXMuc2V0dGluZ3Muc3BlZWR9bXMgJHt0aGlzLnNldHRpbmdzLmVhc2luZ31gfSk7XG4gICAgICAgICAgICBpZih0aGlzLnNldHRpbmdzLmdsYXJlKSB0aGlzLmdsYXJlRWxlbWVudC5jc3Moeyd0cmFuc2l0aW9uJzogYG9wYWNpdHkgJHt0aGlzLnNldHRpbmdzLnNwZWVkfW1zICR7dGhpcy5zZXR0aW5ncy5lYXNpbmd9YH0pO1xuICAgICAgICAgICAgdGhpcy50aW1lb3V0ID0gc2V0VGltZW91dCgoKSA9PiB7XG4gICAgICAgICAgICAgICAgJCh0aGlzKS5jc3Moeyd0cmFuc2l0aW9uJzogJyd9KTtcbiAgICAgICAgICAgICAgICBpZih0aGlzLnNldHRpbmdzLmdsYXJlKSB0aGlzLmdsYXJlRWxlbWVudC5jc3Moeyd0cmFuc2l0aW9uJzogJyd9KTtcbiAgICAgICAgICAgIH0sIHRoaXMuc2V0dGluZ3Muc3BlZWQpO1xuICAgICAgICB9O1xuXG4gICAgICAgIC8qKlxuICAgICAgICAgKiBXaGVuIHVzZXIgbW91c2UgZW50ZXJzIHRpbHQgZWxlbWVudFxuICAgICAgICAgKi9cbiAgICAgICAgY29uc3QgbW91c2VFbnRlciA9IGZ1bmN0aW9uKGV2ZW50KSB7XG4gICAgICAgICAgICB0aGlzLnRpY2tpbmcgPSBmYWxzZTtcbiAgICAgICAgICAgICQodGhpcykuY3NzKHsnd2lsbC1jaGFuZ2UnOiAndHJhbnNmb3JtJ30pO1xuICAgICAgICAgICAgc2V0VHJhbnNpdGlvbi5jYWxsKHRoaXMpO1xuXG4gICAgICAgICAgICAvLyBUcmlnZ2VyIGNoYW5nZSBldmVudFxuICAgICAgICAgICAgJCh0aGlzKS50cmlnZ2VyKFwidGlsdC5tb3VzZUVudGVyXCIpO1xuICAgICAgICB9O1xuXG4gICAgICAgIC8qKlxuICAgICAgICAgKiBSZXR1cm4gdGhlIHgseSBwb3NpdGlvbiBvZiB0aGUgbW91c2Ugb24gdGhlIHRpbHQgZWxlbWVudFxuICAgICAgICAgKiBAcmV0dXJucyB7e3g6ICosIHk6ICp9fVxuICAgICAgICAgKi9cbiAgICAgICAgY29uc3QgZ2V0TW91c2VQb3NpdGlvbnMgPSBmdW5jdGlvbihldmVudCkge1xuICAgICAgICAgICAgaWYgKHR5cGVvZihldmVudCkgPT09IFwidW5kZWZpbmVkXCIpIHtcbiAgICAgICAgICAgICAgICBldmVudCA9IHtcbiAgICAgICAgICAgICAgICAgICAgcGFnZVg6ICQodGhpcykub2Zmc2V0KCkubGVmdCArICQodGhpcykub3V0ZXJXaWR0aCgpIC8gMixcbiAgICAgICAgICAgICAgICAgICAgcGFnZVk6ICQodGhpcykub2Zmc2V0KCkudG9wICsgJCh0aGlzKS5vdXRlckhlaWdodCgpIC8gMlxuICAgICAgICAgICAgICAgIH07XG4gICAgICAgICAgICB9XG4gICAgICAgICAgICByZXR1cm4ge3g6IGV2ZW50LnBhZ2VYLCB5OiBldmVudC5wYWdlWX07XG4gICAgICAgIH07XG5cbiAgICAgICAgLyoqXG4gICAgICAgICAqIFdoZW4gdXNlciBtb3VzZSBtb3ZlcyBvdmVyIHRoZSB0aWx0IGVsZW1lbnRcbiAgICAgICAgICovXG4gICAgICAgIGNvbnN0IG1vdXNlTW92ZSA9IGZ1bmN0aW9uKGV2ZW50KSB7XG4gICAgICAgICAgICB0aGlzLm1vdXNlUG9zaXRpb25zID0gZ2V0TW91c2VQb3NpdGlvbnMoZXZlbnQpO1xuICAgICAgICAgICAgcmVxdWVzdFRpY2suY2FsbCh0aGlzKTtcbiAgICAgICAgfTtcblxuICAgICAgICAvKipcbiAgICAgICAgICogV2hlbiB1c2VyIG1vdXNlIGxlYXZlcyB0aWx0IGVsZW1lbnRcbiAgICAgICAgICovXG4gICAgICAgIGNvbnN0IG1vdXNlTGVhdmUgPSBmdW5jdGlvbigpIHtcbiAgICAgICAgICAgIHNldFRyYW5zaXRpb24uY2FsbCh0aGlzKTtcbiAgICAgICAgICAgIHRoaXMucmVzZXQgPSB0cnVlO1xuICAgICAgICAgICAgcmVxdWVzdFRpY2suY2FsbCh0aGlzKTtcblxuICAgICAgICAgICAgLy8gVHJpZ2dlciBjaGFuZ2UgZXZlbnRcbiAgICAgICAgICAgICQodGhpcykudHJpZ2dlcihcInRpbHQubW91c2VMZWF2ZVwiKTtcbiAgICAgICAgfTtcblxuICAgICAgICAvKipcbiAgICAgICAgICogR2V0IHRpbHQgdmFsdWVzXG4gICAgICAgICAqXG4gICAgICAgICAqIEByZXR1cm5zIHt7eDogdGlsdCB2YWx1ZSwgeTogdGlsdCB2YWx1ZX19XG4gICAgICAgICAqL1xuICAgICAgICBjb25zdCBnZXRWYWx1ZXMgPSBmdW5jdGlvbigpIHtcbiAgICAgICAgICAgIGNvbnN0IHdpZHRoID0gJCh0aGlzKS5vdXRlcldpZHRoKCk7XG4gICAgICAgICAgICBjb25zdCBoZWlnaHQgPSAkKHRoaXMpLm91dGVySGVpZ2h0KCk7XG4gICAgICAgICAgICBjb25zdCBsZWZ0ID0gJCh0aGlzKS5vZmZzZXQoKS5sZWZ0O1xuICAgICAgICAgICAgY29uc3QgdG9wID0gJCh0aGlzKS5vZmZzZXQoKS50b3A7XG4gICAgICAgICAgICBjb25zdCBwZXJjZW50YWdlWCA9ICh0aGlzLm1vdXNlUG9zaXRpb25zLnggLSBsZWZ0KSAvIHdpZHRoO1xuICAgICAgICAgICAgY29uc3QgcGVyY2VudGFnZVkgPSAodGhpcy5tb3VzZVBvc2l0aW9ucy55IC0gdG9wKSAvIGhlaWdodDtcbiAgICAgICAgICAgIC8vIHggb3IgeSBwb3NpdGlvbiBpbnNpZGUgaW5zdGFuY2UgLyB3aWR0aCBvZiBpbnN0YW5jZSA9IHBlcmNlbnRhZ2Ugb2YgcG9zaXRpb24gaW5zaWRlIGluc3RhbmNlICogdGhlIG1heCB0aWx0IHZhbHVlXG4gICAgICAgICAgICBjb25zdCB0aWx0WCA9ICgodGhpcy5zZXR0aW5ncy5tYXhUaWx0IC8gMikgLSAoKHBlcmNlbnRhZ2VYKSAqIHRoaXMuc2V0dGluZ3MubWF4VGlsdCkpLnRvRml4ZWQoMik7XG4gICAgICAgICAgICBjb25zdCB0aWx0WSA9ICgoKHBlcmNlbnRhZ2VZKSAqIHRoaXMuc2V0dGluZ3MubWF4VGlsdCkgLSAodGhpcy5zZXR0aW5ncy5tYXhUaWx0IC8gMikpLnRvRml4ZWQoMik7XG4gICAgICAgICAgICAvLyBhbmdsZVxuICAgICAgICAgICAgY29uc3QgYW5nbGUgPSBNYXRoLmF0YW4yKHRoaXMubW91c2VQb3NpdGlvbnMueCAtIChsZWZ0K3dpZHRoLzIpLC0gKHRoaXMubW91c2VQb3NpdGlvbnMueSAtICh0b3AraGVpZ2h0LzIpKSApKigxODAvTWF0aC5QSSk7XG4gICAgICAgICAgICAvLyBSZXR1cm4geCAmIHkgdGlsdCB2YWx1ZXNcbiAgICAgICAgICAgIHJldHVybiB7dGlsdFgsIHRpbHRZLCAncGVyY2VudGFnZVgnOiBwZXJjZW50YWdlWCAqIDEwMCwgJ3BlcmNlbnRhZ2VZJzogcGVyY2VudGFnZVkgKiAxMDAsIGFuZ2xlfTtcbiAgICAgICAgfTtcblxuICAgICAgICAvKipcbiAgICAgICAgICogVXBkYXRlIHRpbHQgdHJhbnNmb3JtcyBvbiBtb3VzZW1vdmVcbiAgICAgICAgICovXG4gICAgICAgIGNvbnN0IHVwZGF0ZVRyYW5zZm9ybXMgPSBmdW5jdGlvbigpIHtcbiAgICAgICAgICAgIHRoaXMudHJhbnNmb3JtcyA9IGdldFZhbHVlcy5jYWxsKHRoaXMpO1xuXG4gICAgICAgICAgICBpZiAodGhpcy5yZXNldCkge1xuICAgICAgICAgICAgICAgIHRoaXMucmVzZXQgPSBmYWxzZTtcbiAgICAgICAgICAgICAgICAkKHRoaXMpLmNzcygndHJhbnNmb3JtJywgYHBlcnNwZWN0aXZlKCR7dGhpcy5zZXR0aW5ncy5wZXJzcGVjdGl2ZX1weCkgcm90YXRlWCgwZGVnKSByb3RhdGVZKDBkZWcpYCk7XG5cbiAgICAgICAgICAgICAgICAvLyBSb3RhdGUgZ2xhcmUgaWYgZW5hYmxlZFxuICAgICAgICAgICAgICAgIGlmICh0aGlzLnNldHRpbmdzLmdsYXJlKXtcbiAgICAgICAgICAgICAgICAgICAgdGhpcy5nbGFyZUVsZW1lbnQuY3NzKCd0cmFuc2Zvcm0nLCBgcm90YXRlKDE4MGRlZykgdHJhbnNsYXRlKC01MCUsIC01MCUpYCk7XG4gICAgICAgICAgICAgICAgICAgIHRoaXMuZ2xhcmVFbGVtZW50LmNzcygnb3BhY2l0eScsIGAwYCk7XG4gICAgICAgICAgICAgICAgfVxuXG4gICAgICAgICAgICAgICAgcmV0dXJuO1xuICAgICAgICAgICAgfSBlbHNlIHtcbiAgICAgICAgICAgICAgICAkKHRoaXMpLmNzcygndHJhbnNmb3JtJywgYHBlcnNwZWN0aXZlKCR7dGhpcy5zZXR0aW5ncy5wZXJzcGVjdGl2ZX1weCkgcm90YXRlWCgke3RoaXMuc2V0dGluZ3MuZGlzYWJsZUF4aXMgPT09ICd4JyA/IDAgOiB0aGlzLnRyYW5zZm9ybXMudGlsdFl9ZGVnKSByb3RhdGVZKCR7dGhpcy5zZXR0aW5ncy5kaXNhYmxlQXhpcyA9PT0gJ3knID8gMCA6IHRoaXMudHJhbnNmb3Jtcy50aWx0WH1kZWcpIHNjYWxlM2QoJHt0aGlzLnNldHRpbmdzLnNjYWxlfSwke3RoaXMuc2V0dGluZ3Muc2NhbGV9LCR7dGhpcy5zZXR0aW5ncy5zY2FsZX0pYCk7XG5cbiAgICAgICAgICAgICAgICAvLyBSb3RhdGUgZ2xhcmUgaWYgZW5hYmxlZFxuICAgICAgICAgICAgICAgIGlmICh0aGlzLnNldHRpbmdzLmdsYXJlKXtcbiAgICAgICAgICAgICAgICAgICAgdGhpcy5nbGFyZUVsZW1lbnQuY3NzKCd0cmFuc2Zvcm0nLCBgcm90YXRlKCR7dGhpcy50cmFuc2Zvcm1zLmFuZ2xlfWRlZykgdHJhbnNsYXRlKC01MCUsIC01MCUpYCk7XG4gICAgICAgICAgICAgICAgICAgIHRoaXMuZ2xhcmVFbGVtZW50LmNzcygnb3BhY2l0eScsIGAke3RoaXMudHJhbnNmb3Jtcy5wZXJjZW50YWdlWSAqIHRoaXMuc2V0dGluZ3MubWF4R2xhcmUgLyAxMDB9YCk7XG4gICAgICAgICAgICAgICAgfVxuICAgICAgICAgICAgfVxuXG4gICAgICAgICAgICAvLyBUcmlnZ2VyIGNoYW5nZSBldmVudFxuICAgICAgICAgICAgJCh0aGlzKS50cmlnZ2VyKFwiY2hhbmdlXCIsIFt0aGlzLnRyYW5zZm9ybXNdKTtcblxuICAgICAgICAgICAgdGhpcy50aWNraW5nID0gZmFsc2U7XG4gICAgICAgIH07XG5cbiAgICAgICAgLyoqXG4gICAgICAgICAqIFByZXBhcmUgZWxlbWVudHNcbiAgICAgICAgICovXG4gICAgICAgIGNvbnN0IHByZXBhcmVHbGFyZSA9IGZ1bmN0aW9uICgpIHtcbiAgICAgICAgICAgIGNvbnN0IGdsYXJlUHJlcmVuZGVyID0gdGhpcy5zZXR0aW5ncy5nbGFyZVByZXJlbmRlcjtcblxuICAgICAgICAgICAgLy8gSWYgb3B0aW9uIHByZS1yZW5kZXIgaXMgZW5hYmxlZCB3ZSBhc3N1bWUgYWxsIGh0bWwvY3NzIGlzIHByZXNlbnQgZm9yIGFuIG9wdGltYWwgZ2xhcmUgZWZmZWN0LlxuICAgICAgICAgICAgaWYgKCFnbGFyZVByZXJlbmRlcilcbiAgICAgICAgICAgIC8vIENyZWF0ZSBnbGFyZSBlbGVtZW50XG4gICAgICAgICAgICAgICAgJCh0aGlzKS5hcHBlbmQoJzxkaXYgY2xhc3M9XCJqcy10aWx0LWdsYXJlXCI+PGRpdiBjbGFzcz1cImpzLXRpbHQtZ2xhcmUtaW5uZXJcIj48L2Rpdj48L2Rpdj4nKTtcblxuICAgICAgICAgICAgLy8gU3RvcmUgZ2xhcmUgc2VsZWN0b3IgaWYgZ2xhcmUgaXMgZW5hYmxlZFxuICAgICAgICAgICAgdGhpcy5nbGFyZUVsZW1lbnRXcmFwcGVyID0gJCh0aGlzKS5maW5kKFwiLmpzLXRpbHQtZ2xhcmVcIik7XG4gICAgICAgICAgICB0aGlzLmdsYXJlRWxlbWVudCA9ICQodGhpcykuZmluZChcIi5qcy10aWx0LWdsYXJlLWlubmVyXCIpO1xuXG4gICAgICAgICAgICAvLyBSZW1lbWJlcj8gV2UgYXNzdW1lIGFsbCBjc3MgaXMgYWxyZWFkeSBzZXQsIHNvIGp1c3QgcmV0dXJuXG4gICAgICAgICAgICBpZiAoZ2xhcmVQcmVyZW5kZXIpIHJldHVybjtcblxuICAgICAgICAgICAgLy8gQWJzdHJhY3RlZCByZS11c2FibGUgZ2xhcmUgc3R5bGVzXG4gICAgICAgICAgICBjb25zdCBzdHJldGNoID0ge1xuICAgICAgICAgICAgICAgICdwb3NpdGlvbic6ICdhYnNvbHV0ZScsXG4gICAgICAgICAgICAgICAgJ3RvcCc6ICcwJyxcbiAgICAgICAgICAgICAgICAnbGVmdCc6ICcwJyxcbiAgICAgICAgICAgICAgICAnd2lkdGgnOiAnMTAwJScsXG4gICAgICAgICAgICAgICAgJ2hlaWdodCc6ICcxMDAlJyxcbiAgICAgICAgICAgIH07XG5cbiAgICAgICAgICAgIC8vIFN0eWxlIGdsYXJlIHdyYXBwZXJcbiAgICAgICAgICAgIHRoaXMuZ2xhcmVFbGVtZW50V3JhcHBlci5jc3Moc3RyZXRjaCkuY3NzKHtcbiAgICAgICAgICAgICAgICAnb3ZlcmZsb3cnOiAnaGlkZGVuJyxcbiAgICAgICAgICAgICAgICAncG9pbnRlci1ldmVudHMnOiAnbm9uZScsXG4gICAgICAgICAgICB9KTtcblxuICAgICAgICAgICAgLy8gU3R5bGUgZ2xhcmUgZWxlbWVudFxuICAgICAgICAgICAgdGhpcy5nbGFyZUVsZW1lbnQuY3NzKHtcbiAgICAgICAgICAgICAgICAncG9zaXRpb24nOiAnYWJzb2x1dGUnLFxuICAgICAgICAgICAgICAgICd0b3AnOiAnNTAlJyxcbiAgICAgICAgICAgICAgICAnbGVmdCc6ICc1MCUnLFxuICAgICAgICAgICAgICAgICdiYWNrZ3JvdW5kLWltYWdlJzogYGxpbmVhci1ncmFkaWVudCgwZGVnLCByZ2JhKDI1NSwyNTUsMjU1LDApIDAlLCByZ2JhKDI1NSwyNTUsMjU1LDEpIDEwMCUpYCxcbiAgICAgICAgICAgICAgICAnd2lkdGgnOiBgJHskKHRoaXMpLm91dGVyV2lkdGgoKSoyfWAsXG4gICAgICAgICAgICAgICAgJ2hlaWdodCc6IGAkeyQodGhpcykub3V0ZXJXaWR0aCgpKjJ9YCxcbiAgICAgICAgICAgICAgICAndHJhbnNmb3JtJzogJ3JvdGF0ZSgxODBkZWcpIHRyYW5zbGF0ZSgtNTAlLCAtNTAlKScsXG4gICAgICAgICAgICAgICAgJ3RyYW5zZm9ybS1vcmlnaW4nOiAnMCUgMCUnLFxuICAgICAgICAgICAgICAgICdvcGFjaXR5JzogJzAnLFxuICAgICAgICAgICAgfSk7XG5cbiAgICAgICAgfTtcblxuICAgICAgICAvKipcbiAgICAgICAgICogVXBkYXRlIGdsYXJlIG9uIHJlc2l6ZVxuICAgICAgICAgKi9cbiAgICAgICAgY29uc3QgdXBkYXRlR2xhcmVTaXplID0gZnVuY3Rpb24gKCkge1xuICAgICAgICAgICAgdGhpcy5nbGFyZUVsZW1lbnQuY3NzKHtcbiAgICAgICAgICAgICAgICAnd2lkdGgnOiBgJHskKHRoaXMpLm91dGVyV2lkdGgoKSoyfWAsXG4gICAgICAgICAgICAgICAgJ2hlaWdodCc6IGAkeyQodGhpcykub3V0ZXJXaWR0aCgpKjJ9YCxcbiAgICAgICAgICAgIH0pO1xuICAgICAgICB9O1xuXG4gICAgICAgIC8qKlxuICAgICAgICAgKiBQdWJsaWMgbWV0aG9kc1xuICAgICAgICAgKi9cbiAgICAgICAgJC5mbi50aWx0LmRlc3Ryb3kgPSBmdW5jdGlvbigpIHtcbiAgICAgICAgICAgICQodGhpcykuZWFjaChmdW5jdGlvbiAoKSB7XG4gICAgICAgICAgICAgICAgJCh0aGlzKS5maW5kKCcuanMtdGlsdC1nbGFyZScpLnJlbW92ZSgpO1xuICAgICAgICAgICAgICAgICQodGhpcykuY3NzKHsnd2lsbC1jaGFuZ2UnOiAnJywgJ3RyYW5zZm9ybSc6ICcnfSk7XG4gICAgICAgICAgICAgICAgJCh0aGlzKS5vZmYoJ21vdXNlbW92ZSBtb3VzZWVudGVyIG1vdXNlbGVhdmUnKTtcbiAgICAgICAgICAgIH0pO1xuICAgICAgICB9O1xuXG4gICAgICAgICQuZm4udGlsdC5nZXRWYWx1ZXMgPSBmdW5jdGlvbigpIHtcbiAgICAgICAgICAgIGNvbnN0IHJlc3VsdHMgPSBbXTtcbiAgICAgICAgICAgICQodGhpcykuZWFjaChmdW5jdGlvbiAoKSB7XG4gICAgICAgICAgICAgICAgdGhpcy5tb3VzZVBvc2l0aW9ucyA9IGdldE1vdXNlUG9zaXRpb25zLmNhbGwodGhpcyk7XG4gICAgICAgICAgICAgICAgcmVzdWx0cy5wdXNoKGdldFZhbHVlcy5jYWxsKHRoaXMpKTtcbiAgICAgICAgICAgIH0pO1xuICAgICAgICAgICAgcmV0dXJuIHJlc3VsdHM7XG4gICAgICAgIH07XG5cbiAgICAgICAgJC5mbi50aWx0LnJlc2V0ID0gZnVuY3Rpb24oKSB7XG4gICAgICAgICAgICAkKHRoaXMpLmVhY2goZnVuY3Rpb24gKCkge1xuICAgICAgICAgICAgICAgIHRoaXMubW91c2VQb3NpdGlvbnMgPSBnZXRNb3VzZVBvc2l0aW9ucy5jYWxsKHRoaXMpO1xuICAgICAgICAgICAgICAgIHRoaXMuc2V0dGluZ3MgPSAkKHRoaXMpLmRhdGEoJ3NldHRpbmdzJyk7XG4gICAgICAgICAgICAgICAgbW91c2VMZWF2ZS5jYWxsKHRoaXMpO1xuICAgICAgICAgICAgICAgIHNldFRpbWVvdXQoKCkgPT4ge1xuICAgICAgICAgICAgICAgICAgICB0aGlzLnJlc2V0ID0gZmFsc2U7XG4gICAgICAgICAgICAgICAgfSwgdGhpcy5zZXR0aW5ncy50cmFuc2l0aW9uKTtcbiAgICAgICAgICAgIH0pO1xuICAgICAgICB9O1xuXG4gICAgICAgIC8qKlxuICAgICAgICAgKiBMb29wIGV2ZXJ5IGluc3RhbmNlXG4gICAgICAgICAqL1xuICAgICAgICByZXR1cm4gdGhpcy5lYWNoKGZ1bmN0aW9uICgpIHtcblxuICAgICAgICAgICAgLyoqXG4gICAgICAgICAgICAgKiBEZWZhdWx0IHNldHRpbmdzIG1lcmdlZCB3aXRoIHVzZXIgc2V0dGluZ3NcbiAgICAgICAgICAgICAqIENhbiBiZSBzZXQgdHJvdWdoIGRhdGEgYXR0cmlidXRlcyBvciBhcyBwYXJhbWV0ZXIuXG4gICAgICAgICAgICAgKiBAdHlwZSB7Kn1cbiAgICAgICAgICAgICAqL1xuICAgICAgICAgICAgdGhpcy5zZXR0aW5ncyA9ICQuZXh0ZW5kKHtcbiAgICAgICAgICAgICAgICBtYXhUaWx0OiAkKHRoaXMpLmlzKCdbZGF0YS10aWx0LW1heF0nKSA/ICQodGhpcykuZGF0YSgndGlsdC1tYXgnKSA6IDIwLFxuICAgICAgICAgICAgICAgIHBlcnNwZWN0aXZlOiAkKHRoaXMpLmlzKCdbZGF0YS10aWx0LXBlcnNwZWN0aXZlXScpID8gJCh0aGlzKS5kYXRhKCd0aWx0LXBlcnNwZWN0aXZlJykgOiAzMDAsXG4gICAgICAgICAgICAgICAgZWFzaW5nOiAkKHRoaXMpLmlzKCdbZGF0YS10aWx0LWVhc2luZ10nKSA/ICQodGhpcykuZGF0YSgndGlsdC1lYXNpbmcnKSA6ICdjdWJpYy1iZXppZXIoLjAzLC45OCwuNTIsLjk5KScsXG4gICAgICAgICAgICAgICAgc2NhbGU6ICQodGhpcykuaXMoJ1tkYXRhLXRpbHQtc2NhbGVdJykgPyAkKHRoaXMpLmRhdGEoJ3RpbHQtc2NhbGUnKSA6ICcxJyxcbiAgICAgICAgICAgICAgICBzcGVlZDogJCh0aGlzKS5pcygnW2RhdGEtdGlsdC1zcGVlZF0nKSA/ICQodGhpcykuZGF0YSgndGlsdC1zcGVlZCcpIDogJzQwMCcsXG4gICAgICAgICAgICAgICAgdHJhbnNpdGlvbjogJCh0aGlzKS5pcygnW2RhdGEtdGlsdC10cmFuc2l0aW9uXScpID8gJCh0aGlzKS5kYXRhKCd0aWx0LXRyYW5zaXRpb24nKSA6IHRydWUsXG4gICAgICAgICAgICAgICAgZGlzYWJsZUF4aXM6ICQodGhpcykuaXMoJ1tkYXRhLXRpbHQtZGlzYWJsZS1heGlzXScpID8gJCh0aGlzKS5kYXRhKCd0aWx0LWRpc2FibGUtYXhpcycpIDogbnVsbCxcbiAgICAgICAgICAgICAgICBheGlzOiAkKHRoaXMpLmlzKCdbZGF0YS10aWx0LWF4aXNdJykgPyAkKHRoaXMpLmRhdGEoJ3RpbHQtYXhpcycpIDogbnVsbCxcbiAgICAgICAgICAgICAgICByZXNldDogJCh0aGlzKS5pcygnW2RhdGEtdGlsdC1yZXNldF0nKSA/ICQodGhpcykuZGF0YSgndGlsdC1yZXNldCcpIDogdHJ1ZSxcbiAgICAgICAgICAgICAgICBnbGFyZTogJCh0aGlzKS5pcygnW2RhdGEtdGlsdC1nbGFyZV0nKSA/ICQodGhpcykuZGF0YSgndGlsdC1nbGFyZScpIDogZmFsc2UsXG4gICAgICAgICAgICAgICAgbWF4R2xhcmU6ICQodGhpcykuaXMoJ1tkYXRhLXRpbHQtbWF4Z2xhcmVdJykgPyAkKHRoaXMpLmRhdGEoJ3RpbHQtbWF4Z2xhcmUnKSA6IDEsXG4gICAgICAgICAgICB9LCBvcHRpb25zKTtcblxuICAgICAgICAgICAgLy8gQWRkIGRlcHJlY2F0aW9uIHdhcm5pbmcgJiBzZXQgZGlzYWJsZUF4aXMgdG8gZGVwcmVjYXRlZCBheGlzIHNldHRpbmdcbiAgICAgICAgICAgIGlmKHRoaXMuc2V0dGluZ3MuYXhpcyAhPT0gbnVsbCl7XG4gICAgICAgICAgICAgICAgY29uc29sZS53YXJuKCdUaWx0LmpzOiB0aGUgYXhpcyBzZXR0aW5nIGhhcyBiZWVuIHJlbmFtZWQgdG8gZGlzYWJsZUF4aXMuIFNlZSBodHRwczovL2dpdGh1Yi5jb20vZ2lqc3JvZ2UvdGlsdC5qcy9wdWxsLzI2IGZvciBtb3JlIGluZm9ybWF0aW9uJyk7XG4gICAgICAgICAgICAgICAgdGhpcy5zZXR0aW5ncy5kaXNhYmxlQXhpcyA9IHRoaXMuc2V0dGluZ3MuYXhpcztcbiAgICAgICAgICAgIH1cblxuICAgICAgICAgICAgdGhpcy5pbml0ID0gKCkgPT4ge1xuICAgICAgICAgICAgICAgIC8vIFN0b3JlIHNldHRpbmdzXG4gICAgICAgICAgICAgICAgJCh0aGlzKS5kYXRhKCdzZXR0aW5ncycsIHRoaXMuc2V0dGluZ3MpO1xuXG4gICAgICAgICAgICAgICAgLy8gUHJlcGFyZSBlbGVtZW50XG4gICAgICAgICAgICAgICAgaWYodGhpcy5zZXR0aW5ncy5nbGFyZSkgcHJlcGFyZUdsYXJlLmNhbGwodGhpcyk7XG5cbiAgICAgICAgICAgICAgICAvLyBCaW5kIGV2ZW50c1xuICAgICAgICAgICAgICAgIGJpbmRFdmVudHMuY2FsbCh0aGlzKTtcbiAgICAgICAgICAgIH07XG5cbiAgICAgICAgICAgIC8vIEluaXRcbiAgICAgICAgICAgIHRoaXMuaW5pdCgpO1xuXG4gICAgICAgIH0pO1xuICAgIH07XG5cbiAgICAvKipcbiAgICAgKiBBdXRvIGxvYWRcbiAgICAgKi9cbiAgICAkKCdbZGF0YS10aWx0XScpLnRpbHQoKTtcblxuICAgIHJldHVybiB0cnVlO1xufSkpO1xuXG5cbi8vLy8vLy8vLy8vLy8vLy8vL1xuLy8gV0VCUEFDSyBGT09URVJcbi8vIC4vbm9kZV9tb2R1bGVzL3RpbHQuanMvc3JjL3RpbHQuanF1ZXJ5LmpzXG4vLyBtb2R1bGUgaWQgPSAuL25vZGVfbW9kdWxlcy90aWx0LmpzL3NyYy90aWx0LmpxdWVyeS5qc1xuLy8gbW9kdWxlIGNodW5rcyA9IDAiLCJpbXBvcnQgdGlsdCBmcm9tICd0aWx0LmpzJ1xuXG4kKCcubW9kYWwtdG9nZ2xlJykub24oJ2NsaWNrJywgZnVuY3Rpb24oZSkge1xuICBlLnByZXZlbnREZWZhdWx0KCk7XG4gICQoJ2JvZHknKS50b2dnbGVDbGFzcygnb3ZlcmZsb3ctaGlkZGVuJyk7XG4gICQoJy5tb2RhbCcpLnRvZ2dsZUNsYXNzKCdpcy12aXNpYmxlJyk7XG59KTtcblxuJChkb2N1bWVudCkucmVhZHkoZnVuY3Rpb24gKCkge1xuICBjb25zdCB0aWx0ID0gJCgnLmpzLXRpbHQnKS50aWx0KHtcbiAgICBzY2FsZTogMS4yLFxuICAgIGdsYXJlOiB0cnVlLFxuICAgIG1heEdsYXJlOiAuM1xuICB9KTtcbiAgXG4gIC8vIENvbG9yIFBpY2tlciBUb29sIEpzXG4gIGNvbnN0IHRoZW1lU3dpdGNoZXJzID0gZG9jdW1lbnQucXVlcnlTZWxlY3RvckFsbCgnLmRlZmF1bHQtc3dpdGNoJyk7XG4gIGNvbnN0IGR5bmFtaWNJbnB1dHMgPSBkb2N1bWVudC5xdWVyeVNlbGVjdG9yQWxsKCdpbnB1dC5pbnB1dC1jb2xvci1waWNrZXInKTtcbiAgXG4gIGNvbnN0IGhhbmRsZVRoZW1lVXBkYXRlID0gKGNzc1ZhcnMpID0+IHtcbiAgICBjb25zdCByb290ID0gZG9jdW1lbnQucXVlcnlTZWxlY3RvcignOnJvb3QnKTtcbiAgICBjb25zdCBrZXlzID0gT2JqZWN0LmtleXMoY3NzVmFycyk7XG4gICAga2V5cy5mb3JFYWNoKGtleSA9PiB7XG4gICAgICByb290LnN0eWxlLnNldFByb3BlcnR5KGtleSwgY3NzVmFyc1trZXldKTtcbiAgICAgICQuZm4uY2hhbmdlQ29sb3JGb3JtKGtleSwgY3NzVmFyc1trZXldKVxuICAgIH0pO1xuICB9XG4gIFxuICB0aGVtZVN3aXRjaGVycy5mb3JFYWNoKChpdGVtKSA9PiB7XG4gICAgaXRlbS5hZGRFdmVudExpc3RlbmVyKCdjbGljaycsIChlKSA9PiB7XG4gICAgICBjb25zdCBiZ0NvbG9yID0gZS50YXJnZXQuZ2V0QXR0cmlidXRlKCdkYXRhLWJnLWNvbG9yJylcbiAgICAgIGNvbnN0IGNvbG9yID0gZS50YXJnZXQuZ2V0QXR0cmlidXRlKCdkYXRhLWNvbG9yJylcbiAgICAgIGhhbmRsZVRoZW1lVXBkYXRlKHtcbiAgICAgICAgJy0tcHJpbWFyeS1iZy1jb2xvcic6IGJnQ29sb3IsXG4gICAgICAgICctLXByaW1hcnktY29sb3InOiBjb2xvclxuICAgICAgfSk7XG4gICAgICAkKFwiaW5wdXQuaW5wdXQtY29sb3ItcGlja2VyW2RhdGEtaWQ9J2NvbG9yJ11cIikudmFsKGNvbG9yKVxuICAgICAgJChcImlucHV0LmlucHV0LWNvbG9yLXBpY2tlcltkYXRhLWlkPSdiZy1jb2xvciddXCIpLnZhbChiZ0NvbG9yKVxuICAgIH0pO1xuICB9KTtcbiAgXG4gIGR5bmFtaWNJbnB1dHMuZm9yRWFjaCgoaXRlbSkgPT4ge1xuICAgIGl0ZW0uYWRkRXZlbnRMaXN0ZW5lcignaW5wdXQnLCAoZSkgPT4ge1xuICAgICAgY29uc3QgY3NzUHJvcE5hbWUgPSBgLS1wcmltYXJ5LSR7ZS50YXJnZXQuZ2V0QXR0cmlidXRlKCdkYXRhLWlkJyl9YDtcbiAgICAgIGhhbmRsZVRoZW1lVXBkYXRlKHtcbiAgICAgICAgW2Nzc1Byb3BOYW1lXTogZS50YXJnZXQudmFsdWVcbiAgICAgIH0pO1xuICAgIH0pO1xuICB9KTtcbiAgXG4gICQuZm4uY2hhbmdlQ29sb3JGb3JtID0gZnVuY3Rpb24gKGtleSwgY29sb3IpIHtcbiAgICAkKGBmb3JtW25hbWU9Y29udGFjdF0gaW5wdXRbbmFtZT0nJHtrZXl9J11gKS52YWwoY29sb3IpXG4gIH07XG4gIFxufSk7XG5cblxuXG4vLyBXRUJQQUNLIEZPT1RFUiAvL1xuLy8gLi9zcmMvanMvYXBwLmpzIiwiLy8gcmVtb3ZlZCBieSBleHRyYWN0LXRleHQtd2VicGFjay1wbHVnaW5cblxuXG4vLy8vLy8vLy8vLy8vLy8vLy9cbi8vIFdFQlBBQ0sgRk9PVEVSXG4vLyAuL3NyYy9zYXNzL2FwcC5zY3NzXG4vLyBtb2R1bGUgaWQgPSAuL3NyYy9zYXNzL2FwcC5zY3NzXG4vLyBtb2R1bGUgY2h1bmtzID0gMCIsIi8vIHJlbW92ZWQgYnkgZXh0cmFjdC10ZXh0LXdlYnBhY2stcGx1Z2luXG5cblxuLy8vLy8vLy8vLy8vLy8vLy8vXG4vLyBXRUJQQUNLIEZPT1RFUlxuLy8gLi9zcmMvc2Fzcy90YWlsd2luZC5jc3Ncbi8vIG1vZHVsZSBpZCA9IC4vc3JjL3Nhc3MvdGFpbHdpbmQuY3NzXG4vLyBtb2R1bGUgY2h1bmtzID0gMCJdLCJzb3VyY2VSb290IjoiIn0=