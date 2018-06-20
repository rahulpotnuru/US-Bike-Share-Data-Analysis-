# US-Bike-Share-Data-Analysis-
from datetime import datetime
import pandas as pd
import numpy as np
#from datetime import timedelta
import time
CITY_DATA = { 'chicago': 'chicago.csv',
              'new york': 'new_york_city.csv',
              'washington': 'washington.csv' }
def get_city():
    """
    Function:
        Asks user to specify a city to analyse.
    Args:
        None
    Returns:
        (str) city - name of the city to analyse.
    """
    # TO DO: get user input for city (chicago, new york city, washington). HINT: Use a while loop to handle invalid inputs
    cities = ['Chicago','New York','Newyork','Washington']
    while True:
        try:
            city = input("\nWould you like to see data for Chicago, New york, or Washington?")
            if city.title() not in cities:
                print("\nPlease enter a valid city.")
                city = input('\nWould you like to see data for Chicago, New york, or Washington?')
            break
        except:
            print("\nLooks like it is not a valid input.")
    if city.title() == 'Newyork':
        city = city[0:3] + ' ' + city[3:]
    return city.lower()
def get_month():
    """
    Function:
        Asks user to specify a month to filter the data.
    Args:
        None
    Returns:
        (str)month - name of the month to filter the data.
    """
    month = ''
    numbers = ['1','2','3','4','5','6','7','8','9','0']
    months = ['January', 'February', 'March', 'April', 'May', 'June']
    if month not in months:
        while True:
             try:
                    month = input("\nWhich month? January, February, March, April, May or June?")
                    if month in numbers:
                        print('\nPlease do not enter the month in numbers')
                    if month.title() not in months:
                        print("\nPlease enter a month between January and June")
                        while True:
                            try:
                                month = input("\nWhich month? January, February, March, April, May or June?")
                                break
                            except:
                                print("\nLooks like it is not a valid input.")
                    break
             except:
                print("\nLooks like it is not a valid input")
    return month
def get_day():
    """
    Function:
        Asks user to specify a day to filter the data
    Args:
        None
    Returns:
        (str)day - name of the day to filter the data.
    """
    while True:
        try:
            day = int(input("\nWhich day? Please type your response as an integer.(e.g.,sunday = 1)"))
            break
        except:
            print("\nLooks like it is not a valid input.")
    return day
def get_filter():
    '''
    Function:
        Asks user to apply a filter
    Args:
        None
    Returns:
        specific filter entered by the user
    '''
    filter = ''
    filters = ['month','day','none']
    if filter not in filters:
        while True:
            try:
                filter = input("\nWould you like to filter the data by month, day, or not at all? Type 'none' for no time filter.")
                if filter.lower() not in filters:
                    print("\nInvalid filter!")
                    filter = input("\nWould you like to filter the data by month, day, or not at all? Type 'none' for no time filter.")
                break
            except:
                print("\nLooks like it is not a valid input.")
    #if filter == 'none':
     #   filter  = filter.title()
    return filter
def get_data():
    """
    Function:
        Asks user to specify a city, month, and day to analyze.
    Args:
        None
    Returns:
        (str) city - name of the city to analyze
        (str) month - name of the month to filter by, or "all" to apply no month filter
        (str) day - name of the day of week to filter by, or "all" to apply no day filter
        (str) filter - filter specified by the user
    """
    print('\nHello! Let\'s explore some US bikeshare data!')
    # TO DO: get user input for city (chicago, new york city, washington). HINT: Use a while loop to handle invalid inputs
    city = get_city()
    filter = get_filter()
    if filter == 'month':
        # TO DO: get user input for month (all, january, february, ... , june)
        month = get_month()
        day = None
    elif filter == 'day':
        # TO DO: get user input for day of week (all, monday, tuesday, ... sunday)
        day = get_day()
        month = None
    else:
        month,day = None,None
    print('-'*40)
    return city,month,day,filter   


def load_data(city, month, day,filter):
    """
    Function:
        Loads data for the specified city and filters by month and day if applicable.

    Args:
        (str) city - name of the city to analyze
        (str) month - name of the month to filter by, or "all" to apply no month filter
        (str) day - name of the day of week to filter by, or "all" to apply no day filter
        (str) filter - specific filter that entered by user
    Returns:
        df - Pandas DataFrame containing city data filtered by month and day
    """
    
    df = pd.read_csv(CITY_DATA[city])
    df.fillna(0)
    if filter == 'none':
        df_filter = df
    elif filter == 'month':
        months = {'january':1,'february':2,'march':3,'april':4,'may':5,'june':6}
        df['Start Time'] = pd.to_datetime(df['Start Time'])
        df['month'] = df['Start Time'].dt.month
        df_filter = df[df['month'] == months[month]]
    else:
        days = {1:'Sunday',2:'Monday',3:'Tuesday',4:'Wednesday',5:'Thursday',6:'Friday',7:'Saturday'}
        df['Start Time'] = pd.to_datetime(df['Start Time'])
        df['day'] = df['Start Time'].dt.weekday_name
        df_filter = df[df['day'] == days[day]]
    return df_filter


def time_stats(df,filter):
    """
    Function:
        Displays statistics on the most frequent times of travel.
    Args:
        df - python data frame
        filter - specific filter that entered by user
    Returns:
        None
    """
    print('Calculating The Most Frequent Times of Travel...\n')
    start_time = time.time()
    # TO DO: display the most common month
    if filter == 'day' or filter == 'none':
        months = ['January', 'February', 'March', 'April', 'May', 'June']
        df['Start Time'] = pd.to_datetime(df['Start Time'])
        index = df['Start Time'].dt.month.mode()[0]
        most_pop_month = months[index - 1]
        print('\nMost popular month: {}'.format(most_pop_month))

    # TO DO: display the most common day of week
    if filter == 'month' or filter == 'none':
        days_of_week = ['Monday', 'Tuesday', 'Wednesday', 'Thursday', 'Friday',
                    'Saturday', 'Sunday']
        df['Start Time'] = pd.to_datetime(df['Start Time'])
        index = df['Start Time'].dt.dayofweek.mode()[0]
        most_pop_day = days_of_week[index]
        print('\nMost popular day: {}'.format(most_pop_day))
    # TO DO: display the most common start hour
    if filter == 'month' or filter == 'day' or filter == 'none':
        df['Start Time'] = pd.to_datetime(df['Start Time'])
        most_pop_hour = df['Start Time'].dt.hour.mode()[0]
        if most_pop_hour == 0:
            meridiem = 'am'
            most_pop_hour = 12
        elif 1 <= most_pop_hour < 13:
            meridiem = 'am'
        elif 13 <= most_pop_hour < 24:
            meridiem = 'pm'
            most_pop_hour -= 12
        print('\nThe most popular hour of day for start time is {}{}.'.format(most_pop_hour, meridiem))

    print("\nThis took %s seconds." % (time.time() - start_time))
    print('-'*40)


def station_stats(df):
    """Function:
            Displays statistics on the most popular stations and trip.
       Args:
            df - Pandas data frame filtered by month or day
       Returns:
            None
    """

    print('\nCalculating The Most Popular Stations and Trip...\n')
    start_time = time.time()
    
    # TO DO: display most commonly used start station
    pop_start_station = df['Start Station'].mode()[0]
    print('\nThe most popular start station is: {}'.format(pop_start_station))
    # TO DO: display most commonly used end station
    pop_end_station = df['End Station'].mode()[0]
    print('\nThe most popular end station is: {}'.format(pop_end_station))

    # TO DO: display most frequent combination of start station and end station trip
    df['trip'] = df['Start Station'] + ' to ' + df['End Station']
    pop_trip = df['trip'].mode()[0]
    print('\nThe most popular trip is: {}'.format(pop_trip))
    print("\nThis took %s seconds." % (time.time() - start_time))
    print('-'*40)


def trip_duration_stats(df):
    """Function:
            Displays statistics on the total and average trip duration.
       Args:
            df - Pandas data frame filtered by month or day
       Returns:
            None
    """
    
    print('\nCalculating Trip Duration...\n')
    start_time = time.time()
    
    # TO DO: display total travel time
    total_trip_duration = df['Trip Duration'].sum()
    remain_seconds = total_trip_duration % 60
    minutes = total_trip_duration // 60
    remain_minutes = minutes % 60
    hours = minutes // 60
    print('\nThe total trip duration is {} hours, {} minutes, {} seconds'.format(hours,remain_minutes,remain_seconds))
    # TO DO: display mean travel time
    mean_trip_time = df['Trip Duration'].mean()
    remain_seconds = mean_trip_time % 60
    minutes = mean_trip_time // 60
    remain_minutes = minutes % 60
    hours = minutes // 60
    print('\nThe mean trip duration is {} hours, {} minutes, {} seconds'.format(hours,remain_minutes,remain_seconds))
    print("\nThis took %s seconds." % (time.time() - start_time))
    print('-'*40)


def user_stats(df,city):
    """Function:
            Displays statistics on bikeshare users.
       Args:
            df - Pandas data frame filtered by month or day
            city - specific city that entered by user
    """

    print('\nCalculating User Stats...\n')
    start_time = time.time()
    
    # TO DO: Display counts of user types
    df['User'] = df['User Type']
    subscribers = df.query('User == "Subscriber" ').User.count()
    customers = df.query('User == "Customer" ').User.count()
    print('\nThere are {} subscribers and {} customers'.format(subscribers,customers))
    # TO DO: Display counts of gender
    if city == 'chicago' or city == 'new york':
        male = df.query('Gender == "Male" ').Gender.count()
        female = df.query('Gender == "Female" ').Gender.count()
    # TO DO: Display earliest, most recent, and most common year of birth
        earliest_birth = df['Birth Year'].min()
        most_recent_birth = df['Birth Year'].max()
        most_common_birth = df['Birth Year'].mode()[0]
        print('\nOldest user was born in: {}'.format(earliest_birth))
        print('\nYoungest user was born in: {}'.format(most_recent_birth))
        print('\nMost popular birth year is: {}'.format(most_common_birth) )
        print("\nThis took %s seconds." % (time.time() - start_time))
        print('-'*40)
    
def more_data(df,filter):
    '''
    Function:
        Calculates individual trip data
    Args:
        df - Pandas data frame filtered by month or day
        filter - specific filter entered by user
    Returns:
        None
    '''
    if filter == 'month':
        df = df.drop(['trip','User','month'], axis = 1)
        print(df.head(1))
        print('-'*40)
    elif filter == 'day':
        df = df.drop(['trip','User','day'], axis = 1)
        print(df.head(1))
        print('-'*40)
    else:
        df = df.drop(['trip','User'], axis = 1)
        print(df.head(1))
        print('-'*40)
def individual_trip_stats(df,filter):
    '''
    Function:
        Asks if user wants to see individual trip data
    Args:
        df - Pandas data frame filtered by month or day
        filter - specific filter that entered by user
    Returns:
        None
    '''
    start_time = time.time()
    key = True
    while key == True:
        opt_more_data = input('Would you like to see individual trip data?(yes or no)')
        if opt_more_data.lower() == 'yes':
            more_data(df,filter)
        elif opt_more_data.lower() == 'no':
            break
        else:
            print("Looks like it is not a valid input.")
    print('\nThis took %s seconds.'%(time.time() - start_time))
    print('-'*40)

def main():
    while True:
        city, month, day,filter = get_data()
        df = load_data(city, month, day, filter)
        time_stats(df,filter)
        station_stats(df)
        trip_duration_stats(df)
        user_stats(df,city)
        individual_trip_stats(df,filter)
        restart = input('\nWould you like to restart? Enter yes or no.\n')
        if restart.lower() != 'yes':
            break


if __name__ == "__main__":
	main()
