
Winter Storm Uri: Weather vs. Flights

Analyze how Winter Storm Uri affected flight operations at four
major Texas airports during February 2021.

The project combines flight-operation data with hourly weather
observations to investigate whether the storm is visible in aviation
data through cancellations, departure delays, diversions, and
airport-level disruption.

Repository description

Analysis of Winter Storm Uri's impact on flight operations at DFW,
IAH, AUS, and SAT, combining flight and hourly weather data to examine
cancellations, delays, weather conditions, and operational anomalies.

Research questions

The analysis is organized around four main questions:

Can we see Winter Storm Uri in the weather data?

Can we see the weather event in the flight data?

What relationships exist between weather conditions and flight
cancellations?

Are there unusual patterns or anomalies in the flight data?

Study scope

Item                            Scope

Weather event                   Winter Storm Uri
Location                        Texas
Airports                        DFW, IAH, AUS, SAT
Main study period               February 8--22, 2021
Storm period used in analysis   February 14--19, 2021
Main comparison                 January vs. February 2021
Analysis level                  Flight, hourly weather, daily airport operations

Key findings

Winter Storm Uri is visible in flight operations

The flight data shows a strong operational disruption during the storm
period. The clearest signals are:

Flight cancellations increased sharply during the storm.

Snow and ice conditions were associated with substantially higher
cancellation rates.

Departure delays increased during the storm.

The disruption differed across the four airports.

The strongest operational impact occurred around February 14--17,
2021.

Snow and ice were particularly disruptive

The analysis groups detailed weather conditions into five categories:

dry

rain

snow

ice

fog

Raw cancellation counts can be misleading because common conditions such
as overcast weather can naturally account for many flights. Cancellation
rates provide a clearer comparison.

The notebook reports:

January: ice cancellation rate around 29%, snow around 19%.

February: snow around 73%, ice around 55%.

Rain, fog, and dry conditions remained much lower, roughly in the
3--6% range.

This supports the conclusion that snow and ice were important
operational disruptors during the storm.

Temperature and precipitation interact

The analysis compares cancellation rates across temperature bands with
and without snow/ice.

Examples reported in the notebook:

Between -5°C and 0°C, cancellation rates increased from about
21% without snow/ice to 73% with snow/ice.

Between 0°C and 5°C, rates increased from about 9% to 28%.

At temperatures below roughly -5°C, cancellation rates were
already very high, making the independent effects of extreme cold
and precipitation difficult to separate.

The analysis therefore suggests that winter precipitation type is an
important part of the disruption, not temperature alone.

The storm's effects could persist into the following day

Lagged weather analysis compares same-day weather with weather from one
and two days earlier.

During February, the notebook reports that:

Ice R² increased from 0.18 on the same day to 0.30 with a one-day
lag.

Temperature R² increased from 0.49 to 0.51 with a one-day lag.

Snow had its strongest reported relationship on the same day, with
R² around 0.33.

This suggests that some storm effects persisted beyond the hour in which
the weather was first observed.

Several operational anomalies were identified

The notebook investigates three unexpected patterns.

1. The coldest airport had the fewest cancellations

On February 15--16:

DFW reached approximately -18.3°C.

DFW still operated about 29% of departures.

AUS, IAH, and SAT cancelled 100% of departures in the
comparison.

IAH was the mildest of the four airports at approximately
-10.6°C, but was completely shut down.

The notebook also reports that 98% of the 3,939 departures cancelled
during the storm were reported by airlines as weather-related.

The analysis suggests that airport-specific operating conditions
mattered substantially and that temperature alone cannot explain the
differences.

2. Cancellations generally followed the onset of snow and ice

For SAT, DFW, and AUS, mass cancellations began roughly 12 hours after
snow or ice started. IAH reacted much more quickly because it was
affected later.

The notebook notes an important limitation: the data records whether a
flight was cancelled, but not when the cancellation decision was
made, so the analysis cannot determine the exact decision-making
process.

3. A later DFW diversion spike was unrelated to Uri

On February 28, 48 flights bound for DFW were diverted, including
36 scheduled to arrive within a single hour.

This occurred well after the main storm period. The notebook associates
the event with a temporary National Air System / air-traffic-control
restriction, while noting that the exact weather cause is not visible
in the available data.

This serves as an example of why unusual observations should not
automatically be attributed to the main event being studied.

Data

The notebook works with prepared PostgreSQL tables in the team_a
schema:

team_a.prep_flights

team_a.prep_weather_hourly

team_a.prep_airports

The analysis also reads raw BTS CSV files for fields that were not
available in the prepared database tables, including:

cancellation reasons

weather delay

National Air System delay

carrier delay

late-aircraft delay

diversion airport information

The raw files are expected under:

./downloads/**/*.csv

Methodology

1. Load flight and weather data

The notebook connects to PostgreSQL using credentials stored in
environment variables and loads the prepared tables with SQLAlchemy and
pandas.

2. Align flights with hourly weather

Each flight is assigned a scheduled departure timestamp rounded down to
the hour:

flight_date + sched_dep_time → sched_dep_hour

Flights are then joined to weather observations using:

origin = airport_code
sched_dep_hour = timestamp

This associates each scheduled departure with the weather observed at
its origin airport during the scheduled departure hour.

3. Group weather conditions

Detailed weather observations are consolidated into broader categories:

Freezing Rain / Heavy Freezing Rain / Sleet → ice
Light Snowfall / Snowfall / Heavy Snowfall → snow
Rain variants / Thunderstorms / Hail → rain
Fog → fog
Fair / Clear / Cloudy / Overcast → dry

A winter_precip flag identifies observations classified as snow or
ice.

4. Define analysis periods

The notebook uses:

1_before --- before February 14

2_storm --- February 14--19

3_after --- after February 19

Some analyses focus specifically on February 8--22.

5. Compare operational metrics

The main operational metrics include:

cancellation rate

average departure delay

number of departed flights

diverted arrivals

The analysis compares these metrics across:

time

airport

weather condition

temperature bands

January vs. February

same-day vs. lagged weather

6. Correlation and lag analysis

Daily airport-level cancellation rates are compared with weather
variables including:

temperature

humidity

dew point

wind speed

precipitation

Correlation and R² are used to describe statistical relationships.

Correlation does not establish causation, and the notebook
explicitly treats these results as evidence of association rather than
proof of a single causal mechanism.

Visualizations

The notebook produces visualizations covering:

Weather events in the dataset

Weather conditions by period

Weather conditions by airport

Weather conditions over time

Snow and ice conditions over time

Cancellations by weather condition

Cancellation rate by weather condition

Daily cancellation rate and departure delays

Weather/cancellation correlations

Cancellation rate by temperature with and without snow/ice

Same-day vs. lagged weather R²

Airport-level storm anomalies

Cancellation timing relative to snow/ice onset

DFW diversion activity

Tech stack

Python

pandas --- data manipulation and analysis

NumPy --- numerical operations

Matplotlib --- visualization

SQLAlchemy --- PostgreSQL connection

python-dotenv / dotenv_values --- environment configuration

PostgreSQL --- prepared data storage

Jupyter Notebook --- exploratory analysis

Configuration

The notebook expects PostgreSQL connection settings in a .env file.

Example structure:

POSTGRES_USER=your_user
POSTGRES_PASS=your_password
POSTGRES_HOST=your_host
POSTGRES_PORT=5432
POSTGRES_DB=your_database

Do not commit real credentials to the repository.

Running the analysis

1. Clone the repository

git clone <repository-url></repository>
cd <repository-directory></repository>

2. Create a Python environment

python -m venv .venv

Activate it:

macOS/Linux

source .venv/bin/activate

Windows

.venv\Scripts\activate

3. Install dependencies

pip install pandas numpy matplotlib python-dotenv sqlalchemy psycopg2-binary jupyter

4. Configure the database

Create a .env file containing the PostgreSQL connection variables
described above.

Make sure the required team_a tables are available:

team_a.prep_flights
team_a.prep_weather_hourly
team_a.prep_airports

5. Provide raw BTS files

For the anomaly analysis, place the required raw BTS CSV files under:

downloads/

The notebook recursively loads CSV files from that directory.

6. Run the notebook

jupyter notebook demo.ipynb

Run the cells from top to bottom.

Project structure

A suggested repository structure is:

winter-storm-uri-flight-impact/
├── demo.ipynb
├── README.md
├── .env.example
├── .gitignore
├── downloads/
│   └── *.csv
├── images/
│   └── *.png
└── requirements.txt

The current notebook creates analysis images in an images/ directory
during the anomaly-analysis section.

Limitations

Several limitations are important when interpreting the results:

The analysis identifies associations between weather and flight
disruption; it does not establish that weather was the only cause of
every cancellation or delay.

Weather is matched to the scheduled departure hour, not
necessarily the exact conditions at the moment a flight was
cancelled.

Raw cancellation counts depend on how frequently each weather
condition occurs; cancellation rates are therefore more informative
for comparisons.

Temperature and snow/ice effects are difficult to fully separate
during extreme conditions.

The flight data does not contain the exact time at which an airline
made a cancellation decision.

Some anomaly analysis relies on raw BTS fields that are not present
in the prepared database tables.

The February 28 DFW diversion event occurs outside the main Uri
study period and should not be interpreted as evidence of the
storm's impact.

Main takeaway

Winter Storm Uri is clearly visible in the flight data through
increased cancellations, longer departure delays, and airport-level
disruption, with the strongest impact occurring around February
14--17, 2021.

The analysis also shows why looking beyond aggregate cancellation counts
is useful: the airport, weather type, temperature, timing, and
persistence of conditions all contribute to the observed operational
patterns.
