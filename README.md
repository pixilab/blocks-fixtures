Blocks Lighting Fixture Definitions
===================================

A set of lighting fixture definitions usable with PIXILAB Blocks 2 or later.

To use these files, copy the desired brand name folder(s) into the fixtures directory located inside your PIXILAN-root directory.

**NOTE:** These definitions were partially derived from the excellent _Open Fixtures Library_ found at https://open-fixture-library.herokuapp.com

## Folder Structure

The _fixtures_ directory holds a simple folder/file structure:

  * It contains one subdirectory per supported brand, plus one named _Generic_, containing a few common definitions that aren't brand specific.
  * Inside each subdirectory, there's a description file for each fixture, with the fixture's model name. These files have a _.json_ extension, and are formatted according to the JSON standard.

## File Format

The format of the data in each description file is defined by the following types (using _typescript_ notation), where the top level object is of the FixtureDescriptor type:

<pre>
interface FixtureDescriptor {
  channels: Channel[];  // The channels supported by this fixture
  note: string;  // Notes related to this fixture
}

interface Channel extends Named {
  type: ChannelTypes;
  defaultValue?: number; // What value to set channel to on system start-up (not normalized)
  normalized?: boolean; // Exposed as 0...1 value (else with its full range depending on resolution)
  hidden?: boolean; // Not shown in UI (used to represent "gaps" in the channel sequence)
  ranges?: Range[]; // When type is Ranges
}

type ChannelTypes = 'Analog_8'|'Analog_16'|'Analog_24'|'Ranges';

interface Range extends Named {
  first: number; last: number;  // First and last value corresponding to this mode
  discrete?: boolean; // Is discrete (single enum) value only, with no "range" within
}

interface Named {
  name: string;
}
</pre>

Each FixtureDescriptor contains a list of channels. A channel can be of one of four types, where the first three represent an analog value wth 8, 16 or 24 bits resolution (mapping to one, two or three DMX channels). Such an analog value can also accept the following optional properties:

  * **defaultValue** specifies the initial value of the channel. If not specified, the initial value will be zero.
  * **normalized** set to true indicates that the value will be presented as 0...1 in the user interface. If not specified, or set to false, the value will be represented by its full numeric span (e.g., 0...255 for an 8-bit channel, 0...65535 for a 16-bit channel)
  * **hidden** set to true hides the channel from the user interface, thereby blocking out "unused" slots in the channel space.

Alternatively the type is defined as 'Ranges', which describes a channel divided into sub-ranges,  consisting of a number of consecutive values. These ranges are defined in the _ranges_ array, specifying the lower and upper limits of the range. If this range describes a single state (e.g., "strobe off"), the _discrete_ flag is set to indicate this. Otherwise, the range is considered to also have a numberic value, which maps into whatever range is specified (e.g., varying "strobe rates").

Here's an example of what the content of a description file can look like, taken from the generic _Pan Tilt_ model:

<pre>
{
  "note": "Moving Head, 8-bit",
  "channels": [
    {
      "name": "Pan",
      "type": "Analog_8",
      "normalized": true,
      "defaultValue": 127
    }, {
      "name": "Tilt",
      "type": "Analog_8",
      "normalized": true,
      "defaultValue": 127
    }
  ]
}</pre>