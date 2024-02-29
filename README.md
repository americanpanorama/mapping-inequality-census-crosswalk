# mapping-inequality-census-crosswalk

This repo contains spatial files which combine [Mapping Inequality polygons](https://dsl.richmond.edu/panorama/redlining/data) and Census tracts from the [National Historic Geographic Information System](https://www.nhgis.org/) to create a data crosswalk. The files are useful for joining historically redlined areas to demographic data.

## Fields

+ `area_id` (integer) is a unique identifier for each area created by the Digital Scholarship Lab.

+ `city` (string) is the name of the city, town, county, etc.

+ `state` (string) is the 2-letter U.S. Postal Service abbreviation for the state.

+ `city_survey` (boolean, true=1, false=0) denotes whether the map was created as part of the HOLC City Survey Program or not.

+ `cat` (string) is the assigned category from a redlining map. On standard HOLC City Survey Program maps, the category values are “Best”, “Still Desirable”, “Declining”, or “Hazardous”.

+ `grade` (string) is the letter grade used to grade the area. For non-residential areas and most cities that were not part of the City Survey, the value is null.

+ `label` (string) is the label from a redlining map. For most HOLC City Survey Program maps, this value is a letter and number, which often corresponds to an area description viewable on the Mapping Inequality website. 

+ `com` (boolean, true=1, false=0) denotes whether or not an area is labeled explicitly as commercial or inferred to be commercial  from a redlining map.

+ `ind` (boolean, true=1, false=0) denotes whether or not an area is labeled explicitly as industrial, or inferred to be industrial.

+ `res` (boolean, true=1, false=0) denotes whether or not an area is labeled explicitly as residential or inferred to be residential.

+ `fill` (string) is a hexadecimal color code for symbology. The value is typically an approximation of the color shown on a redlining map.

+ `GEOID` (string) is a numeric code that uniquely identifies all administrative/legal and statistical geographic areas for which the Census Bureau tabulates data.

+ `GISJOIN` (string) is a numeric code for joining ables from NHGIS to spatial data.

+ `calc_area` (decimal) the area of the feature in meters.

+ `pct_tract` (decimal) the features's areal percentage of a census tract.

## Steps to create crosswalked data in QGIS

1. On the census tract layer, **Select by Location** features that intersect with Mapping Inequality polygons.

2. **Invert Feature Selection**.

3. Begin an **Edit** session on the census tract layer.  **Delete** the selection. **Save** and stop editing. _This removes tracts that don’t intersect with Mapping Inequality Polygons and speeds up processing._

4. Run the **Union** geoprocess, using Mapping Inequality polygons and the input layer and Census tracts as the overlay layer. _This spits the Mapping Inequality polygons into parts around Census tract boundaries_
 
5. On the newly generated Union layer, **Select by Expression** where `area_id IS NULL`. 

6. Begin an **Edit** session on the Union layer. **Delete** the selection. _This removes the parts of Census tracts that don’t overlap with Mapping Inequality polygons_.

7. **Open Attribute Table** on the Union layer, then **Open Field Calculator**.

8. **Create New Field** with Output field name: `calc_area`, Output type: `decimal number real`, and Expression: `$area`. **Save**. _This calculates the area of each feature in meters._

9. **Create New Field** again, this time with Output field name: `pct_tract`, Output type: `decimal number real`, and Expression: `'calc_area'/'Shape_area'`. **Save**. _This calculates the area percentage that Union feature comprises of the original Census tract._

10. Open **Properties** on the Union layer. Open the **Fields** tab and select fields from the Census layer that are no longer relevant such as `Shape_area` and `Shape_len`. Do not remove `GEOID` and  `GISJOiN`.

11. **Save** and end edit session.

12. If you created a temporary scratch layer for the Union layer, make sure to make the layer permanent before exiting QGIS.

 
