# Google_Distance_formula_in_GSheets
The Google Distance Formula uses the Google Maps API to calculate distances between addresses, ZIP codes, or coordinates. With just a custom formula, we can input the starting and ending points, and voila, the distance is automatically calculated.

It's allows us to automate distance calculations between two locations directly within Google Sheets. No more manual calculations or external tools needed – it's all done seamlessly within the spreadsheet itself!


Simply add the provided JavaScript code to your Google Sheets' script editor. Then, use the custom formulas in your spreadsheet just like any other built-in function.

Code :
/**
 * Calculate the distance between two
 * locations on Google Maps.
 *
 * =GOOGLEMAPS_DISTANCE("NY 10005", "Hoboken NJ", "walking")
 *
 * @param {String} origin The address of starting point
 * @param {String} destination The address of destination
 * @param {String} mode The mode of travel (driving, walking, bicycling or transit)
 * @return {String} The distance in miles
 * @customFunction
 */
const GOOGLEMAPS_DISTANCE = (origin, destination, mode) => {
  const { routes: [data] = [] } = Maps.newDirectionFinder()
    .setOrigin(origin)
    .setDestination(destination)
    .setMode(mode)
    .getDirections();

  if (!data) {
    throw new Error('No route found!');
  }

  const { legs: [{ distance: { text: distance } } = {}] = [] } = data;
  return distance;
};




/**
 * Use Reverse Geocoding to get the address of
 * a point location (latitude, longitude) on Google Maps.
 *
 * =GOOGLEMAPS_REVERSEGEOCODE(latitude, longitude)
 *
 * @param {String} latitude The latitude to lookup.
 * @param {String} longitude The longitude to lookup.
 * @return {String} The postal address of the point.
 * @customFunction
 */

const GOOGLEMAPS_REVERSEGEOCODE = (latitude, longitude) => {
  const { results: [data = {}] = [] } = Maps.newGeocoder().reverseGeocode(latitude, longitude);
  return data.formatted_address;
};


/**
 * Use Reverse Geocoding to get the pin code of
 * a point location (latitude, longitude) on Google Maps.
 *
 * =GOOGLEMAPS_PINCODE(latitude, longitude)
 *
 * @param {String} latitude The latitude to lookup.
 * @param {String} longitude The longitude to lookup.
 * @return {String} The pin code of the point.
 * @customFunction
 */

const GOOGLEMAPS_PINCODE = (latitude, longitude) => {
  const { results: [data = {}] = [] } = Maps.newGeocoder().reverseGeocode(latitude, longitude);
  
  if (!data) {
    throw new Error('No results found!');
  }
  
  const addressComponents = data.address_components;
  for (let i = 0; i < addressComponents.length; i++) {
    const types = addressComponents[i].types;
    if (types.includes('postal_code')) {
      return addressComponents[i].short_name;
    }
  }
  
  throw new Error('Pin code not found!');
};
