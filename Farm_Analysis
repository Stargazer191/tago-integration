const { Analysis, Device, Utils } = require("@tago-io/sdk");

const soiltempLow_threshold = 50;
const soiltempHigh_threshold = 90;
var soiltempGood = "Soil temperature is GOOD";
var soiltempLow  = "Please move your plant to warmer area";
var soiltempHigh = "Plese move your plant to cooler area";

const soilmoistureLow_threshold = 10;
var soilmoistureGood = "Soil moisture is GOOD";
var soilmoistureLow  = "Please water your plant!";

const lightitenity_threshold = 3000;
var lightitensityGood = "Light intensity is GOOD";
var lightitensityLow = "Plant needs more light!";


// The function myAnalysis will run when you execute your analysis
async function myAnalysis(context) {
  // reads the values from the environment and saves it in the variable env_vars
  const env_vars = Utils.envToJson(context.environment);
  if (!env_vars.soil_temp_moisture || !env_vars.light_intensity ||!env_vars.farm_analysis) {
    return context.log("Device token not found on environment parameters");
  }

  const soil_temp_moisture = new Device({ token: env_vars.soil_temp_moisture });
  const light_intensity = new Device({ token: env_vars.light_intensity});
  const farm_analysis = new Device({ token: env_vars.farm_analysis});

  const soiltempAvgFilter = {
    variable: "soil_temperature",
    qty: 1000,
    start_date: "1 hour",
  };

  const soilmoistureAvgFilter = {
    variable: "soil_moisture",
    qty: 1000,
    start_date: "1 hour",
  };

  const lightAvgFilter = {
    variable: "ligh_itensity",
    qty: 1000,
    start_date: "1 hour",
  };
  
  const soiltempAvgArray = await soil_temp_moisture.getData(soiltempAvgFilter);

  if (soiltempAvgArray.length) {
    let soiltempSum = soiltempAvgArray.reduce((previousValue, currentValue) => {
      return previousValue + Number(currentValue.value);
    }, 0);

    soiltempAvg = ((soiltempSum / soiltempAvgArray.length)*9/5)+32;
  } 
  else {
    context.log("No result found for the average soil temp calculation");
  }

  const soilmoistureAvgArray = await soil_temp_moisture.getData(soilmoistureAvgFilter);

  if (soilmoistureAvgArray.length) {
    let soilmoistureSum = soilmoistureAvgArray.reduce((previousValue, currentValue) => {
      return previousValue + Number(currentValue.value);
    }, 0);

    soilmoistureAvg = soilmoistureSum / soilmoistureAvgArray.length;
  }
  else {
    context.log("No result found for the average soil moisture calculation");
  }

  const lightAvgArray = await light_intensity.getData(lightAvgFilter);

  if (lightAvgArray.length) {
    let lightSum = lightAvgArray.reduce((previousValue, currentValue) => {
      return previousValue + Number(currentValue.value);
    }, 0);

    lightAvg = lightSum/ lightAvgArray.length;
  }
  else {
    context.log("No result found for the average light intensity calculation");
  }
 ///////////////////////////////////////////SOIL TEMP CHECK/////////////////////////////////////
  if (soiltempAvg < soiltempLow_threshold){
    const soiltempRec = {
      variable: "soilTemp_rec",
      value: soiltempLow,
    };
    await farm_analysis
      .sendData(soiltempRec)
      .then(context.log("Soil temperature recommendations updated"));
  }
  else if (soiltempAvg >= soiltempLow_threshold && soiltempAvg < soiltempHigh_threshold){
    const soiltempRec = {
      variable: "soilTemp_rec",
      value: soiltempGood,
    };
    await farm_analysis
      .sendData(soiltempRec)
      .then(context.log("Soil temperature recommendations updated"));
  }
  else {
      const soiltempRec = {
      variable: "soilTemp_rec",
      value: soiltempHigh,
    };
    await farm_analysis
      .sendData(soiltempRec)
      .then(context.log("Soil temperature recommendations updated"));
  }

   ///////////////////////////////////////////SOIL MOISTURE CHECK/////////////////////////////////////
  if (soilmoistureAvg < soilmoistureLow_threshold){
    const soilmoistureRec = {
      variable: "soilMoisture_rec",
      value: soilmoistureLow,
    };
    await farm_analysis
      .sendData(soilmoistureRec)
      .then(context.log("Soil moisture recommendations updated"));
  }
  else {
    const soilmoistureRec= {
      variable: "soilMoisture_rec",
      value: soilmoistureGood,
    };
    await farm_analysis
      .sendData(soilmoistureRec)
      .then(context.log("Soil moisture recommendations updated"));
  }

///////////////////////////////////////////LIGHT INTENSITY CHECK/////////////////////////////////////
  if (lightAvg < lightitenity_threshold){
    const lightitenityRec = {
      variable: "lightItenity_rec",
      value: lightitensityLow,
    };
    await farm_analysis
      .sendData(lightitenityRec)
      .then(context.log("Light intensity recommendations updated"));
  }
  else {
    const lightitenityRec= {
      variable: "lightItenity_rec",
      value: lightitensityGood,
    };
    await farm_analysis
      .sendData(lightitenityRec)
      .then(context.log("Light intensity recommendations updated"));
  }
}

module.exports = new Analysis(myAnalysis);
