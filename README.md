# Under-5 Mortality Rate Analysis (UNICEF Dataset)

This project analyzes the global Under-5 Mortality Rate (U5MR) data from UNICEF between 2000 and 2023. It includes data processing, filtering for median estimates, visualizations, and exportable insights such as top 10 countries with the highest rates and trend analysis for Niger.

# Code

import pandas as pd
import matplotlib.pyplot as plt
import seaborn as sns
import os

# 1. Load the CSV file (skip metadata rows)
file_path = "Under-five_Mortality_Rates_2024.csv"
df = pd.read_csv(file_path, skiprows=15, header=None)

# 2. Assign column names dynamically
total_cols = df.shape[1]
meta_cols = ['ISO3', 'Country', 'Region1', 'Region2', 'Region3', 'Region4', 'EstimateType',
             'Unused1', 'Unused2', 'Unused3', 'Unused4', 'Unused5', 'Unused6', 'Unused7']
year_start_index = len(meta_cols)
year_cols = list(range(2000, 2000 + (total_cols - year_start_index)))
df.columns = meta_cols + year_cols

# 3. Filter for Median estimates only
df = df[df['EstimateType'] == 'Median']

# 4. Reshape to long format
df_long = df.melt(id_vars=['Country', 'ISO3'], value_vars=year_cols,
                  var_name='Year', value_name='U5MR')
df_long['Year'] = df_long['Year'].astype(int)

# 5. Drop missing values
df_long = df_long.dropna(subset=['U5MR'])

# 6. Analysis
latest_year = df_long[df_long['Year'] <= 2023]['Year'].max()
latest_data = df_long[df_long['Year'] == latest_year]
top_10 = latest_data.sort_values('U5MR', ascending=False).head(10)
global_avg = latest_data['U5MR'].mean()
niger_data = df_long[(df_long['Country'] == 'Niger') &
                     (df_long['Year'] >= 2000) & (df_long['Year'] <= 2023)]

# 7. Visualization
plt.figure(figsize=(14, 10))

# Top 10 Countries Bar Chart
plt.subplot(2, 1, 1)
sns.barplot(x='U5MR', y='Country', data=top_10, palette='Reds_d',
            hue='Country', legend=False)
plt.title(f'Top 10 Countries with Highest Under-5 Mortality Rate ({latest_year})', fontweight='bold')
plt.xlabel('Mortality Rate per 1000 live births', fontweight='bold')
plt.ylabel('Country', fontweight='bold')
plt.axvline(global_avg, color='blue', linestyle='--', label=f'Global Avg: {global_avg:.1f}')
plt.legend()

# U5MR Trend for Niger
plt.subplot(2, 1, 2)
sns.lineplot(x='Year', y='U5MR', data=niger_data.sort_values('Year'),
             marker='o', color='green')
plt.title('U5MR Trend in Niger (2000–2023)', fontweight='bold')
plt.xlabel('Year', fontweight='bold')
plt.ylabel('Mortality Rate', fontweight='bold')
plt.grid(True)

plt.tight_layout()
plt.savefig('unicef_u5mr_analysis.png', dpi=300)
print("📊 Chart saved as 'unicef_u5mr_analysis.png'.")

# 8. Save data
top_10.to_csv('top10_u5mr_countries.csv', index=False)
print("📁 Top 10 data saved as 'top10_u5mr_countries.csv'.")

# 9. Additional outputs for confirmation
print("\n🟢 Script ran successfully!")
print(f"🌍 Global average U5MR ({latest_year}): {global_avg:.2f}")
print(f"📌 Top 10 countries with highest U5MR in {latest_year}:\n")
print(top_10[['Country', 'U5MR']].to_string(index=False))
print("\n📈 Years included for Niger:", niger_data['Year'].tolist())

#output

📊 Visualization:
![U5MR Chart](unicef_u5mr_analysis.png)

📁 Data:
- [Under-five_Mortality_Rates_2024.csv](Under-five_Mortality_Rates_2024.csv)
  
📄 Report:
- [Under5_Mortality_Report.pdf](Under5_Mortality_Report.pdf)
