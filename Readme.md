# Social Media Analytics MapReduce Workflow

This repository implements a comprehensive MapReduce-based workflow to analyze social media data. The solution processes two datasets: `social_media_logs.txt` and `user_profiles.txt` to derive user behavior patterns, identify trending content, and generate detailed analytics by merging the datasets.

## Overview of the System

The system is structured into five core components:

1. **Data Cleansing and Parsing** - Extracts and validates raw data fields.
2. **Action Aggregation and Sorting** - Aggregates user actions and sorts by post count.
3. **Trending Content Identification** - Identifies the most engaging content.
4. **Dataset Joining** - Merges user activity data with profile details.
5. **Data Visualization** - Presents analytics through both static and interactive visualizations.

## System Design

The pipeline-based architecture processes input data through a MapReduce simulator, where each MapReduce task generates outputs that serve as inputs for subsequent steps. The final outputs are used to create visualizations and perform further analysis.

## Requirements

For a Windows/PyCharm setup, ensure the following:
- Windows operating system
- Python version 3.6+
- PyCharm IDE (either Community or Professional edition)
- Necessary Python libraries: numpy, psutil, matplotlib, pandas, dash, plotly, scipy

## Project Directory Structure

```
social_media_analytics/
├── config.json                   # Configuration settings
├── local_mapreduce.py            # Local MapReduce simulator
├── workflow_driver.py            # Orchestrates the workflow
├── README.md                     # Project documentation
│
├── data/                         # Input data files
│   ├── social_media_logs.txt     # Dataset of social media logs
│   └── user_profiles.txt         # Dataset of user profiles
│
├── output/                       # Automatically generated output directory
│   ├── cleansed_data.txt         # Output from cleansing job
│   ├── user_activity.txt         # Aggregated user activity
│   ├── trending_content.txt      # Trending content output
│   ├── skew_analysis.json        # Skew detection analysis
│   ├── joined_data.txt           # Merged user activity and profile data
│   └── workflow_summary.txt      # Summary of the workflow execution
│
├── src/                          # Source code files
│   ├── cleansing_mapper.py       # Mapper for cleansing data
│   ├── cleansing_reducer.py      # Reducer for cleansing data
│   ├── action_aggregation_mapper.py    # Mapper for action aggregation
│   ├── action_aggregation_reducer.py   # Reducer for action aggregation
│   ├── trending_content_mapper.py      # Mapper for trending content
│   ├── trending_content_combiner.py    # Combiner for trending content
│   ├── trending_content_reducer.py     # Reducer for trending content
│   ├── join_activity_mapper.py    # Mapper for activity join
│   ├── join_profile_mapper.py     # Mapper for profile join
│   ├── join_reducer.py            # Reducer for join operation
│   ├── skew_detection.py          # Skew detection utility
│   └── memory_monitor.py          # Memory usage monitoring utility
│
└── visualizations/               # Visualization tools
    ├── visualize_analytics.py     # Static visualization generation
    └── analytics_dashboard.py     # Interactive dashboard tool
```

## Getting Started

1. **Clone or download this repository**

2. **Install dependencies:**
   ```
   pip install numpy psutil matplotlib pandas dash plotly scipy
   ```

3. **Execute the full workflow:**
   ```
   python workflow_driver.py --config config.json
   ```

4. **Generate visualizations:**
   ```
   python visualizations/visualize_analytics.py --input-dir output --output-dir visualizations
   ```

5. **Launch the interactive dashboard:**
   ```
   python visualizations/analytics_dashboard.py
   ```

## Detailed Component Breakdown

### 1. Data Cleansing and Parsing

This component processes the `social_media_logs.txt` file:
- Extracts the tab-separated records, extracting fields such as Timestamp, UserID, ActionType, ContentID, and Metadata
- Validates the timestamps and metadata
- Filters out any malformed records
- Tracks discarded records for reporting

Implementation:
- `cleansing_mapper.py` - Handles data validation and filtering
- `cleansing_reducer.py` - Passes validated records to the next stage

### 2. Action Aggregation and Sorting

This component aggregates user actions by type:
- Counts posts, likes, comments, and shares for each user
- Sorts the output by post count in descending order
- Uses composite keys for secondary sorting

Implementation:
- `action_aggregation_mapper.py` - Aggregates user actions
- `action_aggregation_reducer.py` - Sorts and formats the output

### 3. Trending Content Identification

This component identifies the most engaging content:
- Computes the engagement (likes + shares) for each content item
- Sets a trending threshold based on the 90th percentile of engagement scores
- Deals with potential data skew in popular content items

Implementation:
- `trending_content_mapper.py` - Counts likes and shares per content
- `trending_content_combiner.py` - Combines results locally
- `trending_content_reducer.py` - Identifies trending content

### 4. Dataset Joining

This component merges user activity data with profile data:
- Combines user activity with user profile information
- Implements a reduce-side join strategy
- Manages data skew for users with excessive activity

Implementation:
- `join_activity_mapper.py` - Prepares activity data for the join
- `join_profile_mapper.py` - Prepares profile data for the join
- `join_reducer.py` - Executes the join operation
- `skew_detection.py` - Handles skewed data

### 5. Data Visualization

This component generates various visual insights:
- Produces static visualizations (e.g., pie charts, bar charts, heatmaps)
- Provides an interactive dashboard for in-depth exploration of data

Implementation:
- `visualize_analytics.py` - Generates static visualizations
- `analytics_dashboard.py` - Provides a web-based dashboard

## Performance Optimization

Several performance-enhancing techniques have been incorporated:

### Code-Level Optimizations
1. **In-mapper combining** - Reduces intermediate data volume by aggregating data in memory
2. **Efficient data structures** - Uses optimized data structures
3. **Secondary sorting** - Ensures proper sorting with minimal additional passes

### System-Level Optimizations
1. **Skew detection** - Identifies and manages skewed keys
2. **Memory monitoring** - Tracks system resource usage during the execution
3. **Dynamic reducers** - Adjusts the number of reducers based on data size

## Running Individual Jobs

For testing or debugging, you can run the following MapReduce jobs individually:

```
# Data cleansing
python local_mapreduce.py --job cleansing --input-dir data --output-dir output

# Action aggregation
python local_mapreduce.py --job aggregation --input-dir output --output-dir output

# Trending content detection
python local_mapreduce.py --job trending --input-dir output --output-dir output

# Join operation
python local_mapreduce.py --job join --input-dir output --output-dir output
```

## Visualization Tools

### Static Visualizations

The `visualize_analytics.py` script generates various visual files:

- `activity_distribution.png` - Pie chart of activity types
- `top_users.png` - Bar chart of top users
- `trending_content.png` - Bar chart of top trending content
- `activity_correlation.png` - Heatmap of activity correlations
- `engagement_distribution.png` - Histogram of engagement scores

Execute with:
```
python visualizations/visualize_analytics.py --input-dir output --output-dir visualizations
```

### Interactive Dashboard

The `analytics_dashboard.py` provides a web interface for:

- User Activity Overview
- Trending Content Analysis
- Activity Correlation Analysis
- Interactive controls for exploration

Run with:
```
python visualizations/analytics_dashboard.py
```
Then visit http://127.0.0.1:8050/ in your browser.

## Troubleshooting

If issues arise:

1. **Check file paths** - Ensure the paths in `config.json` are correct.
2. **Verify input format** - Ensure data files are formatted correctly.
3. **Check Python environment** - Confirm that required packages are installed.
4. **Review logs** - Check `workflow.log` for detailed error messages.

## License

This project is licensed under the MIT License. See the LICENSE file for more details.
