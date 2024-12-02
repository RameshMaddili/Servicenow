# Servicenow1
(function executeFixScript() { 
    // Initialize GlideRecord for the target table
    var gr = new GlideRecord('sn_customerservice_case');
    gr.addNotNullQuery('u_html_description'); // Query records with non-null 'u_html_description'
    gr.query();

    // Iterate over matching records
    while (gr.next()) {
        // Retrieve the HTML content
        var htmlContent = gr.u_html_description.toString();

        // Replace HTML line break tags (<br>, <br/>, <p>) with actual line breaks
        htmlContent = htmlContent.replace(/<\/?(br|p)[^>]*>/gi, "\n");

        // Strip remaining HTML tags
        var plainText = htmlContent.replace(/<\/?[^>]+>/g, "");

        // Decode HTML entities for @, ', and "
        plainText = plainText
            .replace(/&#34;/g, '"')   // Double quotes
            .replace(/&#39;/g, "'")   // Single quotes
            .replace(/&apos;/g, "'")  // Alternative single quotes
            .replace(/&amp;/g, "&")   // Ampersand
            .replace(/&#64;/g, "@");  // At symbol

        // Update the plain text field
        gr.u_plain_description = plainText.trim(); // Remove trailing spaces
        gr.update(); // Save the changes

        // Log for debugging purposes
        gs.print("Updated record: " + gr.sys_id + " with plain text: " + plainText);
    }

    // Log confirmation message
    gs.print("Fix Script completed: HTML content has been successfully converted with @, ', and \" included.");
})();

