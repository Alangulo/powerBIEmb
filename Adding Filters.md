# Adding Filters



function _Report_SetFilters() {
    const filter = {
        $schema: "http://powerbi.com/product/schema#basic",
        target: {
            table: "tgt_state_drug",
            column: "drug_generic_name"
        },
        operator: "In",
        values: ["DILTIAZEM HCL"]
    };
    
    // Get a reference to the embedded report HTML element
    var embedContainer = $('#embedContainer')[0];

    // Get a reference to the embedded report.
    report = powerbi.get(embedContainer);

    // Set the filter for the report.
    // Pay attention that setFilters receives an array.
    report.setFilters([filter])
        .then(function () {
            Log.logText("Report filter was set.");
        })
        .catch(function (errors) {
            Log.log(errors);
        });
}
    
 function _Embed_BasicEmbed() {
   
   // Read embed application token from textbox
    var txtAccessToken = $('#txtAccessToken').val();

    // Read embed URL from textbox
    var txtEmbedUrl = $('#txtReportEmbed').val();

    // Read report Id from textbox
    var txtEmbedReportId = $('#txtEmbedReportId').val();

    // Read embed type from radio
    var tokenType = $('input:radio[name=tokenType]:checked').val();

    var models = window['powerbi-client'].models;

    var permissions = models.Permissions.All;

   
    var config = {
        type: 'report',
        tokenType: tokenType == '0' ? models.TokenType.Aad : models.TokenType.Embed,
        accessToken: txtAccessToken,
        embedUrl: txtEmbedUrl,
        id: txtEmbedReportId,
        permissions: permissions,
        settings: {
          panes: {
            filters: {
              visible: true
            },
            pageNavigation: {
              visible: true
            }
          }
        }
    };

    // Get a reference to the embedded report HTML element
    var embedContainer = $('#embedContainer')[0];

    // Embed the report and display it within the div container.
    var report = powerbi.embed(embedContainer, config);

    // Report.off removes a given event handler if it exists.
    report.off("loaded");

    // Report.on will add an event handler which prints to Log window.
    report.on("loaded", function () {
        Log.logText("Loaded");
    });

    // Report.off removes a given event handler if it exists.
    report.off("rendered");

    // Report.on will add an event handler which prints to Log window.
    report.on("rendered", function () {
        Log.logText("Rendered");
    });

    report.on("error", function (event) {
        Log.log(event.detail);

        report.off("error");
    });

    report.off("saved");
    report.on("saved", function (event) {
        Log.log(event.detail);
        if (event.detail.saveAs) {
            Log.logText('In order to interact with the new report, create a new token and load the new report');
        }
    });
}


