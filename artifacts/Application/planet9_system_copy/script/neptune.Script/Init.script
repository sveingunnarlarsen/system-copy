tabData.getBinding("items").sort(new sap.ui.model.Sorter("name", false, false));
inSystemDestination.getBinding("items").sort(new sap.ui.model.Sorter("name", false, false));

$.ajax({
    type: "POST",
    contentType: "application/json",
    url: "/api/functions/Systems/List",
    success: function (data) {
        data.splice(0, 1, { name: "" })
        modelinSystemDestination.setData(data);
    },
    error: function (result, status) {
        console.log(error);
    }
});
