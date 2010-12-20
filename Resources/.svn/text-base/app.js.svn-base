// this sets the background color of the master UIView (when there are no windows/tab groups on it)
Titanium.UI.setBackgroundColor('#000');

var AppContainer = Titanium.UI.createWindow( {
    title : "Application window"
});

Ti.include('google_ads.js');

function App() {

    App.prototype.detailWindow = Ti.UI.createWindow( {
        title : "test window"
    });
    App.prototype.webView = Ti.UI.createWebView();
    App.prototype.detailWindow.add(this.webView);
    App.prototype.detailWindow.bottom = 50;
    App.prototype.webView.bottom = 50;

    App.prototype.tableView = Titanium.UI.createTableView( {
        data : [ {
            title : "Searching For Data"
        } ]
    });

    //
    // event listener callback when the xhr has completly loaded the data
    // this will update the table with the posts that were found
    //
    App.prototype.tableView.addEventListener('_reload', function(data) {
        Ti.API.info("reload event " + data.data);
        App.prototype.tableView.setData(data.data);
        App.prototype.tableView.bottom = 50;
    });
    App.prototype.tableView.addEventListener('click', function(data) {
        Ti.API.info("click event " + data.index);
        Ti.API.info("click event " + data.row);
        Ti.API.info("click event " + data.row.title);
        App.prototype.detailWindow.title = data.row.title;
        App.prototype.webView.html = data.row.content;
        App.prototype.navGroup.open(App.prototype.detailWindow)
    });
    App.prototype.first = Ti.UI.createWindow( {
        backgroundColor : "#fff",
        title : "My App"
    });

    this.first.add(this.tableView);
    google_ads.displayAd(this.first);

    //
    App.prototype.navGroup = Ti.UI.iPhone.createNavigationGroup( {
        window : this.first
    });

    App.prototype.stringifyParams = function(obj) {
        var str = "";
        for ( var prop in obj) {
            str += (prop + "=" + obj[prop] + "&");
        }
        return str;
    };
    // helper function
    App.xmlElText = function(doc, name) {
        Ti.API.info("IN xmlElText " + name);
        var value = doc.getElementsByTagName(name).item(0).getText();
        Ti.API.debug("IN xmlElText value= " + value);
        return value;
    };
    /**
     *
     */
    App.prototype.doSearch = function(_searchStr) {

        Ti.API.info("IN doSearch ");
        var app = this;

        var searchReq = Titanium.Network.createHTTPClient();
        var url = "http://wsearch.nlm.nih.gov/ws/query";
        var params = {
            db : "healthTopics",
            term : _searchStr,
            retmax : 25
        };

        // titanium doesn't handle params properly with GET
        url = url + "/?" + this.stringifyParams(params)
        searchReq.open("GET", url);

        Ti.API.debug("url: " + url);

        //
        searchReq.onload = function() {

            var doc = Ti.XML.parseString(searchReq.responseText).documentElement;
            var nbrFound = App.xmlElText(doc, "count");
            Ti.API.info("Number found " + nbrFound);

            dataArray = app.loadData(doc);

            AppContainer.add(app.navGroup);

            Ti.API.debug('finished loading data');
            app.tableView.fireEvent('_reload', {
                data : dataArray
            });

            // recommended to clean-up
            searchReq.abort();
            searchReq = null;

        };
        //
        searchReq.onerror = function(e) {
            Ti.API.info("ERROR " + e.error);
        };
        //
        searchReq.onreadystatechange = function(aEvt) {
            Titanium.API.log(" onreadystatechange STATUS CODE "
            + searchReq.status);
        };
        searchReq.send();
    };
    /**
     *
     */
    App.prototype.loadData = function(doc) {
        var items = doc.getElementsByTagName("document");
        var dataArray = [];

        for ( var i = 0; i < items.length; i++) {
            var respItem = items.item(i);

            var row = Ti.UI.createTableViewRow( {
                layout : 'vertical',
                height : 'auto',
                className : "@row"
            });

            // remove any html from the title
            row.title = App.xmlElText(respItem, "content").replace(
            /<\S[^><]*>/g, "");

            // find the fullsummary
            var content = respItem.getElementsByTagName("content");
            for ( var ii = 0; ii < content.length; ii++) {
                var contItem = content.item(ii);
                if (contItem.getAttribute("name") == "FullSummary") {
                    row.content = contItem.getText();
                }
            }

            // row.content = App.xmlElText(respItem,
            // "content").replace(/<\S[^><]*>/g, "");
            dataArray[i] = row;
        }

        return dataArray;
    };
    /**
     * saves the document to the local directory, this will be used to get the
     * data when there is no network around.
     *
     * @param xmlString -
     *            data to be saved locally
     */
    App.prototype.saveXMLDocument = function(xmlString) {
        var fileName = "_xml_" + new Date();
        Ti.API.debug('save XML file ' + fileName);

        var f = Ti.Filesystem.getFile(Ti.Filesystem.applicationDataDirectory,
        fileName);
        f.write(xmlString);
        var path = Ti.Filesystem.applicationDataDirectory
        + Ti.Filesystem.separator + fileName;
        Ti.API.debug('full path ' + path);
        return path;
    };
};

AppContainer.searchText = Titanium.UI.createTextField( {
    color : '#336699',
    top : 30,
    left : 10,
    width : 300,
    height : 40,
    hintText : 'Search Text',
    keyboardType : Titanium.UI.KEYBOARD_DEFAULT,
    returnKeyType : Titanium.UI.RETURNKEY_DEFAULT,
    borderStyle : Titanium.UI.INPUT_BORDERSTYLE_ROUNDED
});
AppContainer.add(AppContainer.searchText);

AppContainer.searchBtn = Titanium.UI.createButton( {
    title : 'Search',
    top : 260,
    width : 130,
    height : 35,
    borderRadius : 1,
    font : {
        fontFamily : 'Arial',
        fontWeight : 'bold',
        fontSize : 14
    }
});

AppContainer.searchBtn.addEventListener('click', function(e) {
    if (AppContainer.searchText.value != '') {
        Ti.API.info("doing search with " + AppContainer.searchText.value);
        var _a = new App();
        _a.doSearch(AppContainer.searchText.value);
    }
});
AppContainer.add(AppContainer.searchBtn);

if (false) {
    var a = new App();

    Ti.API.info(a.navGroup);

    AppContainer.add(a.navGroup);
}

AppContainer.open();
google_ads.displayAd(AppContainer);