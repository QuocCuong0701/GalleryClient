<template>
    <!-- Content -->
    <div class="cd-hero">
        <!-- Navigation -->
        <div class="cd-slider-nav">
            <nav class="navbar">
                <div class="tm-navbar-bg">
                    <a class="navbar-brand text-uppercase a-logo-img" v-bind:href = "clientDomain">
                        <img class="logo-img" :src="require('@/assets/img/gallery.png')"/><span>Gallery</span></a>
                    <button class="navbar-toggler hidden-lg-up" type="button" data-toggle="collapse" data-target="#tmNavbar">
                        &#9776;
                    </button>
                    <div class="collapse navbar-toggleable-md text-xs-center text-uppercase tm-navbar" id="tmNavbar">
                        <ul class="nav navbar-nav">
                            <li class="nav-item">
                                <a class="nav-link" href="#0" data-no="1">
                                    <img class="logo-img" :src="require('@/assets/img/upload.png')"/>
                                </a>
                            </li>
                            <li class="nav-item">
                                <router-link to="/upload" class="nav-link" data-no="2">
                                    <img class="logo-img" :src="require('@/assets/img/upload.png')"/>
                                </router-link>
                            </li>
                        </ul>
                    </div>
                </div>
            </nav>
        </div>
        <ul class="cd-hero-slider">
            <!-- Page 1 Gallery One -->
            <li class="selected">
                <div class="cd-full-width tm-full-width">
                    <div class="container-fluid js-tm-page-content" data-page-no="1" data-page-type="gallery">
                        <div class="tm-img-gallery-container">
                            <div class="tm-img-gallery gallery-one">
                                <!-- Gallery One pop up connected with JS code below -->
                                <div v-for="file in files" :key="file.id" class="grid-item">
                                    <figure class="effect-sadie">
                                        <img v-bind:src = file.fileUriCrop alt="Image" class="img-fluid tm-img" >
                                        <figcaption>
                                            <h2 class="tm-figure-title"><span><strong></strong></span></h2>
                                            <p class="tm-figure-description"></p>
                                            <a v-bind:href = file.fileUriOriginal>View more</a>
                                        </figcaption>
                                    </figure>
                                </div>
                            </div>
                        </div>
                    </div>
                </div>
            </li>
        </ul>

        <footer class="tm-footer">
            <div class="tm-social-icons-container text-xs-center">
                <a href="#" class="tm-social-link"><i class="fa fa-facebook"></i></a>
                <a href="mailto:quoc.cuong.yb.dhtb@gmail.com" class="tm-social-link"><i class="fa fa-google"></i></a>
                <a href="#" class="tm-social-link"><i class="fa fa-twitter"></i></a>
                <a href="https://github.com/QuocCuong0701" target="_blank" class="tm-social-link"><i class="fa fa-github"></i></a>
                <a href="https://www.linkedin.com/in/pquoccuong" target="_blank" class="tm-social-link"><i class="fa fa-linkedin"></i></a>
            </div>
            <p class="tm-copyright-text">Made with
                <img alt="heart" height="20" width="20" v-bind:src="require('@/assets/img/heart.png')"/>
                by <a class="made-by" href="https://github.com/QuocCuong0701" target="_blank">Quoc Cuong</a>
            </p>
        </footer>
    </div>
</template>

<script>
    import $ from 'jquery';
    import 'jquery';
    import 'bootstrap';
    import 'magnific-popup';
    import 'hero-slider';
    import 'tether';
    import axios from "axios";
    import google from 'google-maps';

    const serverDomain = "https://girls-gallery.herokuapp.com/";
    // const serverDomain = "http://localhost:8888";

    export default {
        name: "Index",
        rules: {
            "vue/no-multiple-template-root": "off"
        },
        data() {
            return {
                files: [],
                errors: [],
                // clientDomain: "https://girlgallery.herokuapp.com/"
                clientDomain: "http://localhost:8000"
            }
        },
        created() {
            axios.get(`${serverDomain}/`)
                .then(res => {
                    this.files = res.data.data;
                })
                .catch(e => {
                    this.errors.push(e);
                })
        }
    }

    var tm_gray_site = false;
    if (tm_gray_site) {
        $('html').addClass('gray');
    } else {
        $('html').removeClass('gray');
    }

    function adjustHeightOfPage(pageNo) {
        var pageContentHeight = 0;
        var pageType = $('div[data-page-no="' + pageNo + '"]').data("page-type");

        if (pageType !== undefined && pageType === "gallery") {
            pageContentHeight = $(".cd-hero-slider li:nth-of-type(" + pageNo + ") .tm-img-gallery-container").height();
        } else {
            pageContentHeight = $(".cd-hero-slider li:nth-of-type(" + pageNo + ") .js-tm-page-content").height() + 20;
        }

        // Get the page height
        var totalPageHeight = $('.cd-slider-nav').height() + pageContentHeight + $('.tm-footer').outerHeight();

        // Adjust layout based on page height and window height
        if (totalPageHeight > $(window).height()) {
            $('.cd-hero-slider').addClass('small-screen');
            $('.cd-hero-slider li:nth-of-type(' + pageNo + ')').css("min-height", totalPageHeight + "px");
        } else {
            $('.cd-hero-slider').removeClass('small-screen');
            $('.cd-hero-slider li:nth-of-type(' + pageNo + ')').css("min-height", "100%");
        }
    }

    /*
        Everything is loaded including images.
    */
    $(window).on('load', function () {
        // adjustHeightOfPage(1); // Adjust page height
        /* Gallery One pop up
        -----------------------------------------*/
        $('.gallery-one').magnificPopup({
            delegate: 'a', // child items selector, by clicking on it popup will open
            type: 'image',
            gallery: {enabled: true}
        });

        /* Collapse menu after click
        -----------------------------------------*/
        $('#tmNavbar a').click(function () {
            $('#tmNavbar').collapse('hide');
            adjustHeightOfPage($(this).data("no")); // Adjust page height
        });

        /* Browser resized
        -----------------------------------------*/
        $(window).resize(function () {
            var currentPageNo = $(".cd-hero-slider li.selected .js-tm-page-content").data("page-no");
            setTimeout(function () {
                adjustHeightOfPage(currentPageNo);
            }, 1000);
        });

        // Remove preloader (https://ihatetomatoes.net/create-custom-preloading-screen/)
        $('body').addClass('loaded');
        // Write current year in copyright text.
        $(".tm-copyright-year").text(new Date().getFullYear());

        setTimeout(function () {
            var currentPageNo = $(".cd-hero-slider li.selected .js-tm-page-content").data("page-no");
            adjustHeightOfPage(currentPageNo);
        }, 5000);
    });

    /* Google map
    ------------------------------------------------*/
    var map = '';
    var center;

    // eslint-disable-next-line no-unused-vars
    function initialize() {
        var mapOptions = {
            zoom: 13,
            center: new google.maps.LatLng(37.779724, -122.452152),
            scrollwheel: false
        };

        map = new google.maps.Map(document.getElementById('google-map'), mapOptions);
        google.maps.event.addDomListener(map, 'idle', function () {
            calculateCenter();
        });

        google.maps.event.addDomListener(window, 'resize', function () {
            map.setCenter(center);
        });
    }

    function calculateCenter() {
        center = map.getCenter();
    }
</script>

<style scoped>

</style>