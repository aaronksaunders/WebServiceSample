var google_ads = {
    adUrl : 'google_ads.html',
    adWebView : null,
    parentWindow: null,
    displayAd : function(mainWindow) {
        google_ads.parentWindow = mainWindow;
        var numLoads = 0;
        if (google_ads.adWebView) {
            mainWindow.remove(google_ads.adWebView);
        }
        google_ads.adWebView = Ti.UI.createWebView({
            url: this.adUrl,
            height: 50,
            bottom: 0,
            left: 0,
            right: 0,
            backgroundColor: '#000000',
            borderRadius: 0,
            borderColor: '#000000'
        });

        google_ads.adWebView.addEventListener('load', function (evt) {
            if ( evt.url.indexOf('google_ads.html') == -1) {
                Ti.Platform.openURL(evt.url);
                google_ads.displayAd(google_ads.parentWindow);

            }
        });
        mainWindow.add(google_ads.adWebView);

    }
};
