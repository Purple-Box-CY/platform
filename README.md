# Purple Box Online
Platform for purple-box applications

## Install
1. `cp docker/.env.dist docker/.env`
2. `make setup`
3. `make up`
4. Check links:
- http://localhost/
- http://localhost/api
- http://localhost/cms

## Install WEB
1. `rm -rf sources/web && git clone git@github.com:Purple-Box-CY/web.git sources/web`
2. `brew install yarn`
3. `cp sources/web/.env.dist sources/web/.env`
4. `cd sources/web && yarn install`
5. `yarn build`
6. `yarn start`
7. Check link: http://localhost:3000/
8. Change value *.env* `REACT_APP_API_URL` to `http://localhost/`
9. `yarn start`

## Install API
1. `rm -rf sources/api && git clone git@github.com:Purple-Box-CY/api.git sources/api`
2. `make php`
3. `cd sources/api && composer install`
4. `php bin/console assets:install`
5. Check link: http://localhost/api

## Install CMS
1. `rm -rf sources/cms && git clone git@github.com:Purple-Box-CY/cms.git sources/cms`
2. `make php`
3. `cd sources/cms && composer install`
4. `php bin/console assets:install`
5. Check link: http://localhost/cms

