**Weather Forecast to capture temperature
**
**Scope: 
**
1. Use Ruby on Rails
2. Accept zipcode as input
3. retrieve forecast data for given zip code
4. display temperature details to user
5. caching the details based upon zipcode for 30 mins

**Setup:
**
**Install Ruby
**
$asdf plugin add ruby
$asdf install ruby latest
$asdf global ruby latest

**Install Rails
**
$gem install rails

**App Installation
**
rails new weather-forecast --skip-activerecord # as we are storing any data in database, skipping active record

cd weather-forecast

**Gems used
**
gem 'httparty' #for calling third party services

gem 'bootstrap', '~> 5.3.2' #for form view
gem 'dotenv-rails' #for storing env variables
gem 'rspec-rails' #for unit testing
gem 'faker' #used for generating fake address details

bundle install


**Generated forecast controller
**
def show
  begin
    @zip_code = params["zip_code"]
    forecast_service = ForecastService.new('api_key')
    #caching the forecast request for 30 minutes
    @forecast_cache_exists = Rails.cache.exist?(@zip_code) 
    @forecast = Rails.cache.fetch(@zip_code, expires_in: 30.minutes) do
      forecast_service.get_forecast_data(@zip_code)
    end 
  rescue => e
    flash[:alert] = e.message
  end
end


**Generated Service for getting weather using openweathermap
**
  def get_forecast_data(zip)
    zip_with_country = get_zip_with_country(zip)
    response = self.class.get('/data/2.5/weather', query: {zip:zip_with_country, appid: @api_key })
    puts response.inspect
    if response.success?
      response['main']
    else
      raise response.message
    end
  end


1. this method is used to fetch temperature detials using zip code.
2. api key needs to be created in https://api.openweathermap.org

Generated ForecastHelper for converting kelvin units to celsius

by default openweathermap units is in kelvin



