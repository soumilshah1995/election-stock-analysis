# election-stock-analysis
election-stock-analysis
![newplot](https://github.com/soumilshah1995/election-stock-analysis/assets/39345855/c9585d8a-4eb0-4c35-ad3e-290b2411841a)


# Code
```
mport pandas as pd
import yfinance as yf
import plotly.graph_objs as go
from plotly.subplots import make_subplots
import numpy as np

def plot_stock_price_during_elections(ticker_symbol, output_csv_filename):
    # Fetch the data from Yahoo Finance
    stock_data = yf.download(ticker_symbol, start='1980-01-01', end='2024-12-31')

    # Resample data to monthly frequency
    stock_data_monthly = stock_data.resample('M').mean()

    # Calculate percentage change in closing prices on a monthly basis
    stock_data_monthly['Monthly % Change'] = stock_data_monthly['Close'].pct_change() * 100

    # Define election years and months along with the party elected
    election_periods = [
        ['1951-10-01', '1952-02-29', 'Congress'],
        ['1957-02-01', '1957-03-31', 'Congress'],
        ['1962-02-01', '1962-02-28', 'Congress'],
        ['1967-02-01', '1967-03-31', 'Congress'],
        ['1971-02-01', '1971-03-31', 'Congress'],
        ['1977-03-01', '1977-03-31', 'Janata Party'],
        ['1980-01-01', '1980-01-31', 'Congress'],
        ['1984-12-01', '1984-12-31', 'Congress'],
        ['1989-11-01', '1989-12-31', 'Congress'],
        ['1991-05-01', '1991-06-30', 'Congress'],
        ['1996-04-01', '1996-05-31', 'BJP'],
        ['1998-02-01', '1998-03-31', 'BJP'],
        ['1999-09-01', '1999-10-31', 'BJP'],
        ['2004-04-01', '2004-05-31', 'Congress'],
        ['2009-04-01', '2009-05-31', 'Congress'],
        ['2014-04-01', '2014-05-31', 'BJP'],
        ['2019-04-01', '2019-05-31', 'BJP'],
        ['2024-04-01', '2024-05-31', 'Unknown']
    ]

    # Create a subplot
    fig = make_subplots(rows=1, cols=1)

    # Color the bars based on positive or negative change
    bar_colors = ['red' if x < 0 else 'green' for x in stock_data_monthly['Monthly % Change']]

    # Add bar trace for monthly percentage change
    fig.add_trace(go.Bar(x=stock_data_monthly.index, y=stock_data_monthly['Monthly % Change'],
                         marker_color=bar_colors, name='Monthly % Change'))

    # Add grid lines for election months and markers for parties
    for start, end, party in election_periods:
        fig.add_vline(x=start, line=dict(color="rgba(0, 0, 0, 0.5)", width=1, dash="dashdot"))
        fig.add_vline(x=end, line=dict(color="rgba(0, 0, 0, 0.5)", width=1, dash="dashdot"))
        # Add annotation for the party elected
        fig.add_annotation(x=(pd.to_datetime(start) + pd.to_timedelta((pd.to_datetime(end) - pd.to_datetime(start)) / 2)),
                           y=stock_data_monthly['Monthly % Change'].max(),
                           text=party,
                           showarrow=False,
                           font=dict(color="black", size=16),  # Adjust font size here
                           align="center",
                           yanchor="bottom")

    # Add title and labels
    fig.update_layout(title=f'{ticker_symbol} Stock Price During Election Years and Party Elected with Percentage Gain or Loss',
                      xaxis_title='Date',
                      yaxis_title='Monthly % Change (%)',
                      height=800)  # Adjust height as needed

    # Update x-axis to display month names
    fig.update_xaxes(
        tickmode='array',
        tickvals=stock_data_monthly.index[::2],  # Display every other month for better readability
        ticktext=stock_data_monthly.index[::2].strftime('%b %Y'),
        tickangle=90
    )

    # Add analysis by Soumil Shah
    analysis_text = """
    ## Analysis by Soumil Shah
    """
    fig.add_annotation(
        text=analysis_text,
        align='left',
        showarrow=False,
        xref='paper',
        yref='paper',
        x=0.01,
        y=0.95,
        bordercolor='black',
        borderwidth=1,
        bgcolor='white',
        font=dict(size=10)
    )

    # Save the data to CSV file
    stock_data_monthly.to_csv(output_csv_filename)

    # Display the plot
    fig.show()

# Example usage
plot_stock_price_during_elections('^NSEBANK', 'stock_data.csv')  # You can replace 'AAPL' with any ticker symbol you want

```
