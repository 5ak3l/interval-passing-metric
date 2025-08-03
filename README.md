# interval-passing-metric
A python-written code calculating players/ teams pass completion through 10-minute intervals. This can also be used as a predictive model in performance analysis for quality of passing, fatigue, opponent analysis, accuracy under pressure etc.

    import os
    import xml.etree.ElementTree as ET
    import pandas as pd
    from collections import defaultdict
    time_intervals = [(i, i + 9) for i in range(0, 91, 10)]
    completed_passes = defaultdict(lambda: {interval: 0 for interval in time_intervals})
    unsuccessful_passes = defaultdict(lambda: {interval: 0 for interval in time_intervals})

    teams_dir = {}
    def process_xml_file(file_path):
    tree = ET.parse(file_path)
    root = tree.getroot()
    
    for game in root.findall('.//Game'):
        a_team_id = game.get('away_team_id')
        a_team_name = game.get('away_team_name')
        h_team_id = game.get('home_team_id')
        h_team_name = game.get('home_team_name')

        try:
            if not a_team_id in teams_dir:
                teams_dir[a_team_id] = a_team_name
                teams_dir[h_team_id] = h_team_name
        except:
            pass
    

    for event in root.findall('.//Event'):
        if event.get('type_id') == '1':  # Pass event
            min = int(event.get('min'))
            outcome = event.get('outcome')
            team_id = event.get('team_id')
            
            for interval in time_intervals:
                if interval[0] <= min <= interval[1]:
                    if outcome == '1':  # Completed pass
                        completed_passes[team_id][interval] += 1
                    elif outcome == '0':  # Unsuccessful pass
                        unsuccessful_passes[team_id][interval] += 1
                    break
    import os

    directory = '/Users/user/desktop/F24 La Liga 2023/'
    file_paths = os.listdir(directory)


    for file_path in file_paths:
    file = os.path.join(directory, file_path)
    process_xml_file(file)
    # Pass completion rate for each interval for each team
    pass_completion_rate = defaultdict(dict)
    for team_id in completed_passes:
    for interval in time_intervals:
        total_passes = completed_passes[team_id][interval] + unsuccessful_passes[team_id][interval]
        if total_passes > 0:
            pass_completion_rate[team_id][interval] = completed_passes[team_id][interval] / total_passes
        else:
            pass_completion_rate[team_id][interval] = None
    # DataFrames for better visualization
    team_dataframes = {}
    for team_id in pass_completion_rate:
    data = {
        'Time Interval': [f'{interval[0]}-{interval[1]}' for interval in time_intervals],
        'Completed Passes': [completed_passes[team_id][interval] for interval in time_intervals],
        'Unsuccessful Passes': [unsuccessful_passes[team_id][interval] for interval in time_intervals],
        'Pass Completion Rate': [pass_completion_rate[team_id][interval] for interval in time_intervals],
    }
    team_dataframes[team_id] = pd.DataFrame(data)
    for team_id, df in team_dataframes.items():
    print(f"Team {teams_dir[team_id]} Pass Completion Statistics by Interval:")
    print(df)
    print("\n")
    import matplotlib.pyplot as plt
    import pandas as pd

    team_dataframes = {
    'Athletic Club': pd.DataFrame({
        'Time Interval': ['0-9', '10-19', '20-29', '30-39', '40-49', '50-59', '60-69', '70-79', '80-89', '90-99'],
        'Pass Completion Rate': [0.756588, 0.785564, 0.800413, 0.800900, 0.778899, 0.762983, 0.786418, 0.789245, 0.772118, 0.734831]
    }),
    'Mallorca': pd.DataFrame({
        'Time Interval': ['0-9', '10-19', '20-29', '30-39', '40-49', '50-59', '60-69', '70-79', '80-89', '90-99'],
        'Pass Completion Rate': [0.737267, 0.750785, 0.740791, 0.711811, 0.731359, 0.785227, 0.759285, 0.730985, 0.703968, 0.674359]
    }),
    'Rayo Vallecano': pd.DataFrame({
        'Time Interval': ['0-9', '10-19', '20-29', '30-39', '40-49', '50-59', '60-69', '70-79', '80-89', '90-99'],
        'Pass Completion Rate': [0.757530, 0.783351, 0.775853, 0.777011, 0.770513, 0.775663, 0.784118, 0.779371, 0.752513, 0.694864]
    }),
    'Barcelona': pd.DataFrame({
        'Time Interval': ['0-9', '10-19', '20-29', '30-39', '40-49', '50-59', '60-69', '70-79', '80-89', '90-99'],
        'Pass Completion Rate': [0.870327, 0.875902, 0.867790, 0.872285, 0.860923, 0.851594, 0.870472, 0.869836, 0.863863, 0.790989]
    }),
    'Elche': pd.DataFrame({
        'Time Interval': ['0-9', '10-19', '20-29', '30-39', '40-49', '50-59', '60-69', '70-79', '80-89', '90-99'],
        'Pass Completion Rate': [0.726990, 0.785894, 0.773222, 0.771186, 0.776322, 0.780154, 0.782477, 0.777778, 0.768464, 0.701378]
    }),
    'Real Betis': pd.DataFrame({
        'Time Interval': ['0-9', '10-19', '20-29', '30-39', '40-49', '50-59', '60-69', '70-79', '80-89', '90-99'],
        'Pass Completion Rate': [0.821813, 0.833562, 0.811350, 0.825027, 0.813518, 0.793642, 0.814815, 0.810095, 0.773082, 0.717979]
    }),
    'Espanyol': pd.DataFrame({
        'Time Interval': ['0-9', '10-19', '20-29', '30-39', '40-49', '50-59', '60-69', '70-79', '80-89', '90-99'],
        'Pass Completion Rate': [0.740193, 0.732857, 0.749656, 0.730687, 0.748179, 0.767132, 0.752515, 0.748217, 0.746441, 0.610354]
    }),
    'Celta de Vigo': pd.DataFrame({
        'Time Interval': ['0-9', '10-19', '20-29', '30-39', '40-49', '50-59', '60-69', '70-79', '80-89', '90-99'],
        'Pass Completion Rate': [0.791687, 0.802721, 0.813632, 0.825117, 0.770796, 0.811408, 0.800839, 0.811512, 0.787356, 0.752567]
    }),
    'Real Sociedad': pd.DataFrame({
        'Time Interval': ['0-9', '10-19', '20-29', '30-39', '40-49', '50-59', '60-69', '70-79', '80-89', '90-99'],
        'Pass Completion Rate': [0.811794, 0.813146, 0.825901, 0.823140, 0.776417, 0.815721, 0.833892, 0.803794, 0.774666, 0.697385]
    }),
    'Cádiz': pd.DataFrame({
        'Time Interval': ['0-9', '10-19', '20-29', '30-39', '40-49', '50-59', '60-69', '70-79', '80-89', '90-99'],
        'Pass Completion Rate': [0.709698, 0.736939, 0.710134, 0.720280, 0.695831, 0.674437, 0.747940, 0.726989, 0.743455, 0.679470]
    }),
    'Sevilla': pd.DataFrame({
        'Time Interval': ['0-9', '10-19', '20-29', '30-39', '40-49', '50-59', '60-69', '70-79', '80-89', '90-99'],
        'Pass Completion Rate': [0.813846, 0.826940, 0.814172, 0.822890, 0.788454, 0.811927, 0.807197, 0.791379, 0.798813, 0.745261]
    }),
    'Osasuna': pd.DataFrame({
        'Time Interval': ['0-9', '10-19', '20-29', '30-39', '40-49', '50-59', '60-69', '70-79', '80-89', '90-99'],
        'Pass Completion Rate': [0.755002, 0.786174, 0.742112, 0.755529, 0.756176, 0.761051, 0.779063, 0.772470, 0.738167, 0.709302]
    }),
    'Almería': pd.DataFrame({
        'Time Interval': ['0-9', '10-19', '20-29', '30-39', '40-49', '50-59', '60-69', '70-79', '80-89', '90-99'],
        'Pass Completion Rate': [0.783869, 0.772467, 0.782109, 0.770345, 0.751488, 0.785937, 0.776185, 0.780272, 0.770810, 0.703571]
    }),
    'Real Madrid': pd.DataFrame({
        'Time Interval': ['0-9', '10-19', '20-29', '30-39', '40-49', '50-59', '60-69', '70-79', '80-89', '90-99'],
        'Pass Completion Rate': [0.889949, 0.887193, 0.893625, 0.889299, 0.881169, 0.884243, 0.889561, 0.889517, 0.891210, 0.838622]
    }),
    'Atlético de Madrid': pd.DataFrame({
        'Time Interval': ['0-9', '10-19', '20-29', '30-39', '40-49', '50-59', '60-69', '70-79', '80-89', '90-99'],
        'Pass Completion Rate': [0.832400, 0.846972, 0.840053, 0.826379, 0.806975, 0.818400, 0.825131, 0.839879, 0.811448, 0.737853]
    }),
    'Getafe': pd.DataFrame({
        'Time Interval': ['0-9', '10-19', '20-29', '30-39', '40-49', '50-59', '60-69', '70-79', '80-89', '90-99'],
        'Pass Completion Rate': [0.678299, 0.728313, 0.705341, 0.728592, 0.694661, 0.664918, 0.679970, 0.706320, 0.660837, 0.587278]
    }),
    'Villarreal': pd.DataFrame({
        'Time Interval': ['0-9', '10-19', '20-29', '30-39', '40-49', '50-59', '60-69', '70-79', '80-89', '90-99'],
        'Pass Completion Rate': [0.852074, 0.856736, 0.873379, 0.843825, 0.826688, 0.840844, 0.834774, 0.832280, 0.825806, 0.785417]
    }),
    'Real Valladolid': pd.DataFrame({
        'Time Interval': ['0-9', '10-19', '20-29', '30-39', '40-49', '50-59', '60-69', '70-79', '80-89', '90-99'],
        'Pass Completion Rate': [0.795295, 0.786292, 0.775336, 0.798206, 0.737195, 0.783425, 0.774967, 0.780571, 0.763963, 0.688889]
    }),
    'Valencia CF': pd.DataFrame({
        'Time Interval': ['0-9', '10-19', '20-29', '30-39', '40-49', '50-59', '60-69', '70-79', '80-89', '90-99'],
        'Pass Completion Rate': [0.759250, 0.794604, 0.772874, 0.787827, 0.771664, 0.784290, 0.796471, 0.808721, 0.796339, 0.721619]
    }),
    'Girona': pd.DataFrame({
        'Time Interval': ['0-9', '10-19', '20-29', '30-39', '40-49', '50-59', '60-69', '70-79', '80-89', '90-99'],
        'Pass Completion Rate': [0.846522, 0.841179, 0.835197, 0.822196, 0.799657, 0.827492, 0.813278, 0.803347, 0.807922, 0.740668]
    })
    }  

    # Create curve graphs for each team
    for team_name, df in team_dataframes.items():
    plt.figure(figsize=(10, 6))
    plt.plot(df['Time Interval'], df['Pass Completion Rate'], marker='o', linestyle='-')
    plt.title(f"Pass Completion Rate Over Time for Team {team_name}")
    plt.xlabel("Time Interval")
    plt.ylabel("Pass Completion Rate")
    plt.ylim(0, 1)
    plt.grid(True)
    plt.xticks(rotation=45)
    plt.tight_layout()
    plt.show()
