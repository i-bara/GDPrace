<script setup>

import * as d3 from "https://cdn.jsdelivr.net/npm/d3@7/+esm";

import { ref, onMounted, reactive, watch, watchEffect } from 'vue'

import axios from 'axios'

function assert(condition) {
    if (!condition) {
        throw "Assertion failed";
    }
}

let info = {}
let promises = []

// https://pkgstore.datahub.io/core/country-list/data_json/data/8c458f2d15d9f2119654b29ede6e45b8/data_json.json
// let countries = [{"Code": "AF", "Name": "Afghanistan"},{"Code": "AX", "Name": "\u00c5land Islands"},{"Code": "AL", "Name": "Albania"},{"Code": "DZ", "Name": "Algeria"},{"Code": "AS", "Name": "American Samoa"},{"Code": "AD", "Name": "Andorra"},{"Code": "AO", "Name": "Angola"},{"Code": "AI", "Name": "Anguilla"},{"Code": "AQ", "Name": "Antarctica"},{"Code": "AG", "Name": "Antigua and Barbuda"},{"Code": "AR", "Name": "Argentina"},{"Code": "AM", "Name": "Armenia"},{"Code": "AW", "Name": "Aruba"},{"Code": "AU", "Name": "Australia"},{"Code": "AT", "Name": "Austria"},{"Code": "AZ", "Name": "Azerbaijan"},{"Code": "BS", "Name": "Bahamas"},{"Code": "BH", "Name": "Bahrain"},{"Code": "BD", "Name": "Bangladesh"},{"Code": "BB", "Name": "Barbados"},{"Code": "BY", "Name": "Belarus"},{"Code": "BE", "Name": "Belgium"},{"Code": "BZ", "Name": "Belize"},{"Code": "BJ", "Name": "Benin"},{"Code": "BM", "Name": "Bermuda"},{"Code": "BT", "Name": "Bhutan"},{"Code": "BO", "Name": "Bolivia, Plurinational State of"},{"Code": "BQ", "Name": "Bonaire, Sint Eustatius and Saba"},{"Code": "BA", "Name": "Bosnia and Herzegovina"},{"Code": "BW", "Name": "Botswana"},{"Code": "BV", "Name": "Bouvet Island"},{"Code": "BR", "Name": "Brazil"},{"Code": "IO", "Name": "British Indian Ocean Territory"},{"Code": "BN", "Name": "Brunei Darussalam"},{"Code": "BG", "Name": "Bulgaria"},{"Code": "BF", "Name": "Burkina Faso"},{"Code": "BI", "Name": "Burundi"},{"Code": "KH", "Name": "Cambodia"},{"Code": "CM", "Name": "Cameroon"},{"Code": "CA", "Name": "Canada"},{"Code": "CV", "Name": "Cape Verde"},{"Code": "KY", "Name": "Cayman Islands"},{"Code": "CF", "Name": "Central African Republic"},{"Code": "TD", "Name": "Chad"},{"Code": "CL", "Name": "Chile"},{"Code": "CN", "Name": "China"},{"Code": "CX", "Name": "Christmas Island"},{"Code": "CC", "Name": "Cocos (Keeling) Islands"},{"Code": "CO", "Name": "Colombia"},{"Code": "KM", "Name": "Comoros"},{"Code": "CG", "Name": "Congo"},{"Code": "CD", "Name": "Congo, the Democratic Republic of the"},{"Code": "CK", "Name": "Cook Islands"},{"Code": "CR", "Name": "Costa Rica"},{"Code": "CI", "Name": "C\u00f4te d'Ivoire"},{"Code": "HR", "Name": "Croatia"},{"Code": "CU", "Name": "Cuba"},{"Code": "CW", "Name": "Cura\u00e7ao"},{"Code": "CY", "Name": "Cyprus"},{"Code": "CZ", "Name": "Czech Republic"},{"Code": "DK", "Name": "Denmark"},{"Code": "DJ", "Name": "Djibouti"},{"Code": "DM", "Name": "Dominica"},{"Code": "DO", "Name": "Dominican Republic"},{"Code": "EC", "Name": "Ecuador"},{"Code": "EG", "Name": "Egypt"},{"Code": "SV", "Name": "El Salvador"},{"Code": "GQ", "Name": "Equatorial Guinea"},{"Code": "ER", "Name": "Eritrea"},{"Code": "EE", "Name": "Estonia"},{"Code": "ET", "Name": "Ethiopia"},{"Code": "FK", "Name": "Falkland Islands (Malvinas)"},{"Code": "FO", "Name": "Faroe Islands"},{"Code": "FJ", "Name": "Fiji"},{"Code": "FI", "Name": "Finland"},{"Code": "FR", "Name": "France"},{"Code": "GF", "Name": "French Guiana"},{"Code": "PF", "Name": "French Polynesia"},{"Code": "TF", "Name": "French Southern Territories"},{"Code": "GA", "Name": "Gabon"},{"Code": "GM", "Name": "Gambia"},{"Code": "GE", "Name": "Georgia"},{"Code": "DE", "Name": "Germany"},{"Code": "GH", "Name": "Ghana"},{"Code": "GI", "Name": "Gibraltar"},{"Code": "GR", "Name": "Greece"},{"Code": "GL", "Name": "Greenland"},{"Code": "GD", "Name": "Grenada"},{"Code": "GP", "Name": "Guadeloupe"},{"Code": "GU", "Name": "Guam"},{"Code": "GT", "Name": "Guatemala"},{"Code": "GG", "Name": "Guernsey"},{"Code": "GN", "Name": "Guinea"},{"Code": "GW", "Name": "Guinea-Bissau"},{"Code": "GY", "Name": "Guyana"},{"Code": "HT", "Name": "Haiti"},{"Code": "HM", "Name": "Heard Island and McDonald Islands"},{"Code": "VA", "Name": "Holy See (Vatican City State)"},{"Code": "HN", "Name": "Honduras"},{"Code": "HK", "Name": "Hong Kong"},{"Code": "HU", "Name": "Hungary"},{"Code": "IS", "Name": "Iceland"},{"Code": "IN", "Name": "India"},{"Code": "ID", "Name": "Indonesia"},{"Code": "IR", "Name": "Iran, Islamic Republic of"},{"Code": "IQ", "Name": "Iraq"},{"Code": "IE", "Name": "Ireland"},{"Code": "IM", "Name": "Isle of Man"},{"Code": "IL", "Name": "Israel"},{"Code": "IT", "Name": "Italy"},{"Code": "JM", "Name": "Jamaica"},{"Code": "JP", "Name": "Japan"},{"Code": "JE", "Name": "Jersey"},{"Code": "JO", "Name": "Jordan"},{"Code": "KZ", "Name": "Kazakhstan"},{"Code": "KE", "Name": "Kenya"},{"Code": "KI", "Name": "Kiribati"},{"Code": "KP", "Name": "Korea, Democratic People's Republic of"},{"Code": "KR", "Name": "Korea, Republic of"},{"Code": "KW", "Name": "Kuwait"},{"Code": "KG", "Name": "Kyrgyzstan"},{"Code": "LA", "Name": "Lao People's Democratic Republic"},{"Code": "LV", "Name": "Latvia"},{"Code": "LB", "Name": "Lebanon"},{"Code": "LS", "Name": "Lesotho"},{"Code": "LR", "Name": "Liberia"},{"Code": "LY", "Name": "Libya"},{"Code": "LI", "Name": "Liechtenstein"},{"Code": "LT", "Name": "Lithuania"},{"Code": "LU", "Name": "Luxembourg"},{"Code": "MO", "Name": "Macao"},{"Code": "MK", "Name": "Macedonia, the Former Yugoslav Republic of"},{"Code": "MG", "Name": "Madagascar"},{"Code": "MW", "Name": "Malawi"},{"Code": "MY", "Name": "Malaysia"},{"Code": "MV", "Name": "Maldives"},{"Code": "ML", "Name": "Mali"},{"Code": "MT", "Name": "Malta"},{"Code": "MH", "Name": "Marshall Islands"},{"Code": "MQ", "Name": "Martinique"},{"Code": "MR", "Name": "Mauritania"},{"Code": "MU", "Name": "Mauritius"},{"Code": "YT", "Name": "Mayotte"},{"Code": "MX", "Name": "Mexico"},{"Code": "FM", "Name": "Micronesia, Federated States of"},{"Code": "MD", "Name": "Moldova, Republic of"},{"Code": "MC", "Name": "Monaco"},{"Code": "MN", "Name": "Mongolia"},{"Code": "ME", "Name": "Montenegro"},{"Code": "MS", "Name": "Montserrat"},{"Code": "MA", "Name": "Morocco"},{"Code": "MZ", "Name": "Mozambique"},{"Code": "MM", "Name": "Myanmar"},{"Code": "NA", "Name": "Namibia"},{"Code": "NR", "Name": "Nauru"},{"Code": "NP", "Name": "Nepal"},{"Code": "NL", "Name": "Netherlands"},{"Code": "NC", "Name": "New Caledonia"},{"Code": "NZ", "Name": "New Zealand"},{"Code": "NI", "Name": "Nicaragua"},{"Code": "NE", "Name": "Niger"},{"Code": "NG", "Name": "Nigeria"},{"Code": "NU", "Name": "Niue"},{"Code": "NF", "Name": "Norfolk Island"},{"Code": "MP", "Name": "Northern Mariana Islands"},{"Code": "NO", "Name": "Norway"},{"Code": "OM", "Name": "Oman"},{"Code": "PK", "Name": "Pakistan"},{"Code": "PW", "Name": "Palau"},{"Code": "PS", "Name": "Palestine, State of"},{"Code": "PA", "Name": "Panama"},{"Code": "PG", "Name": "Papua New Guinea"},{"Code": "PY", "Name": "Paraguay"},{"Code": "PE", "Name": "Peru"},{"Code": "PH", "Name": "Philippines"},{"Code": "PN", "Name": "Pitcairn"},{"Code": "PL", "Name": "Poland"},{"Code": "PT", "Name": "Portugal"},{"Code": "PR", "Name": "Puerto Rico"},{"Code": "QA", "Name": "Qatar"},{"Code": "RE", "Name": "R\u00e9union"},{"Code": "RO", "Name": "Romania"},{"Code": "RU", "Name": "Russian Federation"},{"Code": "RW", "Name": "Rwanda"},{"Code": "BL", "Name": "Saint Barth\u00e9lemy"},{"Code": "SH", "Name": "Saint Helena, Ascension and Tristan da Cunha"},{"Code": "KN", "Name": "Saint Kitts and Nevis"},{"Code": "LC", "Name": "Saint Lucia"},{"Code": "MF", "Name": "Saint Martin (French part)"},{"Code": "PM", "Name": "Saint Pierre and Miquelon"},{"Code": "VC", "Name": "Saint Vincent and the Grenadines"},{"Code": "WS", "Name": "Samoa"},{"Code": "SM", "Name": "San Marino"},{"Code": "ST", "Name": "Sao Tome and Principe"},{"Code": "SA", "Name": "Saudi Arabia"},{"Code": "SN", "Name": "Senegal"},{"Code": "RS", "Name": "Serbia"},{"Code": "SC", "Name": "Seychelles"},{"Code": "SL", "Name": "Sierra Leone"},{"Code": "SG", "Name": "Singapore"},{"Code": "SX", "Name": "Sint Maarten (Dutch part)"},{"Code": "SK", "Name": "Slovakia"},{"Code": "SI", "Name": "Slovenia"},{"Code": "SB", "Name": "Solomon Islands"},{"Code": "SO", "Name": "Somalia"},{"Code": "ZA", "Name": "South Africa"},{"Code": "GS", "Name": "South Georgia and the South Sandwich Islands"},{"Code": "SS", "Name": "South Sudan"},{"Code": "ES", "Name": "Spain"},{"Code": "LK", "Name": "Sri Lanka"},{"Code": "SD", "Name": "Sudan"},{"Code": "SR", "Name": "Suriname"},{"Code": "SJ", "Name": "Svalbard and Jan Mayen"},{"Code": "SZ", "Name": "Swaziland"},{"Code": "SE", "Name": "Sweden"},{"Code": "CH", "Name": "Switzerland"},{"Code": "SY", "Name": "Syrian Arab Republic"},{"Code": "TW", "Name": "Taiwan, Province of China"},{"Code": "TJ", "Name": "Tajikistan"},{"Code": "TZ", "Name": "Tanzania, United Republic of"},{"Code": "TH", "Name": "Thailand"},{"Code": "TL", "Name": "Timor-Leste"},{"Code": "TG", "Name": "Togo"},{"Code": "TK", "Name": "Tokelau"},{"Code": "TO", "Name": "Tonga"},{"Code": "TT", "Name": "Trinidad and Tobago"},{"Code": "TN", "Name": "Tunisia"},{"Code": "TR", "Name": "Turkey"},{"Code": "TM", "Name": "Turkmenistan"},{"Code": "TC", "Name": "Turks and Caicos Islands"},{"Code": "TV", "Name": "Tuvalu"},{"Code": "UG", "Name": "Uganda"},{"Code": "UA", "Name": "Ukraine"},{"Code": "AE", "Name": "United Arab Emirates"},{"Code": "GB", "Name": "United Kingdom"},{"Code": "US", "Name": "United States"},{"Code": "UM", "Name": "United States Minor Outlying Islands"},{"Code": "UY", "Name": "Uruguay"},{"Code": "UZ", "Name": "Uzbekistan"},{"Code": "VU", "Name": "Vanuatu"},{"Code": "VE", "Name": "Venezuela, Bolivarian Republic of"},{"Code": "VN", "Name": "Viet Nam"},{"Code": "VG", "Name": "Virgin Islands, British"},{"Code": "VI", "Name": "Virgin Islands, U.S."},{"Code": "WF", "Name": "Wallis and Futuna"},{"Code": "EH", "Name": "Western Sahara"},{"Code": "YE", "Name": "Yemen"},{"Code": "ZM", "Name": "Zambia"},{"Code": "ZW", "Name": "Zimbabwe"}]

// let countryCodes = new Set();
// let countryNames = new Set();
// for (let country of countries) {
//     countryCodes.add(country.Code);
//     countryNames.add(country.Name);
// }

// console.log(countryNames);

// https://github.com/lukes/ISO-3166-Countries-with-Regional-Codes/blob/master/all/all.json
let countries = [{"name":"Afghanistan","alpha-2":"AF","alpha-3":"AFG","country-code":"004","iso_3166-2":"ISO 3166-2:AF","region":"Asia","sub-region":"Southern Asia","intermediate-region":"","region-code":"142","sub-region-code":"034","intermediate-region-code":""},{"name":"Åland Islands","alpha-2":"AX","alpha-3":"ALA","country-code":"248","iso_3166-2":"ISO 3166-2:AX","region":"Europe","sub-region":"Northern Europe","intermediate-region":"","region-code":"150","sub-region-code":"154","intermediate-region-code":""},{"name":"Albania","alpha-2":"AL","alpha-3":"ALB","country-code":"008","iso_3166-2":"ISO 3166-2:AL","region":"Europe","sub-region":"Southern Europe","intermediate-region":"","region-code":"150","sub-region-code":"039","intermediate-region-code":""},{"name":"Algeria","alpha-2":"DZ","alpha-3":"DZA","country-code":"012","iso_3166-2":"ISO 3166-2:DZ","region":"Africa","sub-region":"Northern Africa","intermediate-region":"","region-code":"002","sub-region-code":"015","intermediate-region-code":""},{"name":"American Samoa","alpha-2":"AS","alpha-3":"ASM","country-code":"016","iso_3166-2":"ISO 3166-2:AS","region":"Oceania","sub-region":"Polynesia","intermediate-region":"","region-code":"009","sub-region-code":"061","intermediate-region-code":""},{"name":"Andorra","alpha-2":"AD","alpha-3":"AND","country-code":"020","iso_3166-2":"ISO 3166-2:AD","region":"Europe","sub-region":"Southern Europe","intermediate-region":"","region-code":"150","sub-region-code":"039","intermediate-region-code":""},{"name":"Angola","alpha-2":"AO","alpha-3":"AGO","country-code":"024","iso_3166-2":"ISO 3166-2:AO","region":"Africa","sub-region":"Sub-Saharan Africa","intermediate-region":"Middle Africa","region-code":"002","sub-region-code":"202","intermediate-region-code":"017"},{"name":"Anguilla","alpha-2":"AI","alpha-3":"AIA","country-code":"660","iso_3166-2":"ISO 3166-2:AI","region":"Americas","sub-region":"Latin America and the Caribbean","intermediate-region":"Caribbean","region-code":"019","sub-region-code":"419","intermediate-region-code":"029"},{"name":"Antarctica","alpha-2":"AQ","alpha-3":"ATA","country-code":"010","iso_3166-2":"ISO 3166-2:AQ","region":"","sub-region":"","intermediate-region":"","region-code":"","sub-region-code":"","intermediate-region-code":""},{"name":"Antigua and Barbuda","alpha-2":"AG","alpha-3":"ATG","country-code":"028","iso_3166-2":"ISO 3166-2:AG","region":"Americas","sub-region":"Latin America and the Caribbean","intermediate-region":"Caribbean","region-code":"019","sub-region-code":"419","intermediate-region-code":"029"},{"name":"Argentina","alpha-2":"AR","alpha-3":"ARG","country-code":"032","iso_3166-2":"ISO 3166-2:AR","region":"Americas","sub-region":"Latin America and the Caribbean","intermediate-region":"South America","region-code":"019","sub-region-code":"419","intermediate-region-code":"005"},{"name":"Armenia","alpha-2":"AM","alpha-3":"ARM","country-code":"051","iso_3166-2":"ISO 3166-2:AM","region":"Asia","sub-region":"Western Asia","intermediate-region":"","region-code":"142","sub-region-code":"145","intermediate-region-code":""},{"name":"Aruba","alpha-2":"AW","alpha-3":"ABW","country-code":"533","iso_3166-2":"ISO 3166-2:AW","region":"Americas","sub-region":"Latin America and the Caribbean","intermediate-region":"Caribbean","region-code":"019","sub-region-code":"419","intermediate-region-code":"029"},{"name":"Australia","alpha-2":"AU","alpha-3":"AUS","country-code":"036","iso_3166-2":"ISO 3166-2:AU","region":"Oceania","sub-region":"Australia and New Zealand","intermediate-region":"","region-code":"009","sub-region-code":"053","intermediate-region-code":""},{"name":"Austria","alpha-2":"AT","alpha-3":"AUT","country-code":"040","iso_3166-2":"ISO 3166-2:AT","region":"Europe","sub-region":"Western Europe","intermediate-region":"","region-code":"150","sub-region-code":"155","intermediate-region-code":""},{"name":"Azerbaijan","alpha-2":"AZ","alpha-3":"AZE","country-code":"031","iso_3166-2":"ISO 3166-2:AZ","region":"Asia","sub-region":"Western Asia","intermediate-region":"","region-code":"142","sub-region-code":"145","intermediate-region-code":""},{"name":"Bahamas","alpha-2":"BS","alpha-3":"BHS","country-code":"044","iso_3166-2":"ISO 3166-2:BS","region":"Americas","sub-region":"Latin America and the Caribbean","intermediate-region":"Caribbean","region-code":"019","sub-region-code":"419","intermediate-region-code":"029"},{"name":"Bahrain","alpha-2":"BH","alpha-3":"BHR","country-code":"048","iso_3166-2":"ISO 3166-2:BH","region":"Asia","sub-region":"Western Asia","intermediate-region":"","region-code":"142","sub-region-code":"145","intermediate-region-code":""},{"name":"Bangladesh","alpha-2":"BD","alpha-3":"BGD","country-code":"050","iso_3166-2":"ISO 3166-2:BD","region":"Asia","sub-region":"Southern Asia","intermediate-region":"","region-code":"142","sub-region-code":"034","intermediate-region-code":""},{"name":"Barbados","alpha-2":"BB","alpha-3":"BRB","country-code":"052","iso_3166-2":"ISO 3166-2:BB","region":"Americas","sub-region":"Latin America and the Caribbean","intermediate-region":"Caribbean","region-code":"019","sub-region-code":"419","intermediate-region-code":"029"},{"name":"Belarus","alpha-2":"BY","alpha-3":"BLR","country-code":"112","iso_3166-2":"ISO 3166-2:BY","region":"Europe","sub-region":"Eastern Europe","intermediate-region":"","region-code":"150","sub-region-code":"151","intermediate-region-code":""},{"name":"Belgium","alpha-2":"BE","alpha-3":"BEL","country-code":"056","iso_3166-2":"ISO 3166-2:BE","region":"Europe","sub-region":"Western Europe","intermediate-region":"","region-code":"150","sub-region-code":"155","intermediate-region-code":""},{"name":"Belize","alpha-2":"BZ","alpha-3":"BLZ","country-code":"084","iso_3166-2":"ISO 3166-2:BZ","region":"Americas","sub-region":"Latin America and the Caribbean","intermediate-region":"Central America","region-code":"019","sub-region-code":"419","intermediate-region-code":"013"},{"name":"Benin","alpha-2":"BJ","alpha-3":"BEN","country-code":"204","iso_3166-2":"ISO 3166-2:BJ","region":"Africa","sub-region":"Sub-Saharan Africa","intermediate-region":"Western Africa","region-code":"002","sub-region-code":"202","intermediate-region-code":"011"},{"name":"Bermuda","alpha-2":"BM","alpha-3":"BMU","country-code":"060","iso_3166-2":"ISO 3166-2:BM","region":"Americas","sub-region":"Northern America","intermediate-region":"","region-code":"019","sub-region-code":"021","intermediate-region-code":""},{"name":"Bhutan","alpha-2":"BT","alpha-3":"BTN","country-code":"064","iso_3166-2":"ISO 3166-2:BT","region":"Asia","sub-region":"Southern Asia","intermediate-region":"","region-code":"142","sub-region-code":"034","intermediate-region-code":""},{"name":"Bolivia (Plurinational State of)","alpha-2":"BO","alpha-3":"BOL","country-code":"068","iso_3166-2":"ISO 3166-2:BO","region":"Americas","sub-region":"Latin America and the Caribbean","intermediate-region":"South America","region-code":"019","sub-region-code":"419","intermediate-region-code":"005"},{"name":"Bonaire, Sint Eustatius and Saba","alpha-2":"BQ","alpha-3":"BES","country-code":"535","iso_3166-2":"ISO 3166-2:BQ","region":"Americas","sub-region":"Latin America and the Caribbean","intermediate-region":"Caribbean","region-code":"019","sub-region-code":"419","intermediate-region-code":"029"},{"name":"Bosnia and Herzegovina","alpha-2":"BA","alpha-3":"BIH","country-code":"070","iso_3166-2":"ISO 3166-2:BA","region":"Europe","sub-region":"Southern Europe","intermediate-region":"","region-code":"150","sub-region-code":"039","intermediate-region-code":""},{"name":"Botswana","alpha-2":"BW","alpha-3":"BWA","country-code":"072","iso_3166-2":"ISO 3166-2:BW","region":"Africa","sub-region":"Sub-Saharan Africa","intermediate-region":"Southern Africa","region-code":"002","sub-region-code":"202","intermediate-region-code":"018"},{"name":"Bouvet Island","alpha-2":"BV","alpha-3":"BVT","country-code":"074","iso_3166-2":"ISO 3166-2:BV","region":"Americas","sub-region":"Latin America and the Caribbean","intermediate-region":"South America","region-code":"019","sub-region-code":"419","intermediate-region-code":"005"},{"name":"Brazil","alpha-2":"BR","alpha-3":"BRA","country-code":"076","iso_3166-2":"ISO 3166-2:BR","region":"Americas","sub-region":"Latin America and the Caribbean","intermediate-region":"South America","region-code":"019","sub-region-code":"419","intermediate-region-code":"005"},{"name":"British Indian Ocean Territory","alpha-2":"IO","alpha-3":"IOT","country-code":"086","iso_3166-2":"ISO 3166-2:IO","region":"Africa","sub-region":"Sub-Saharan Africa","intermediate-region":"Eastern Africa","region-code":"002","sub-region-code":"202","intermediate-region-code":"014"},{"name":"Brunei Darussalam","alpha-2":"BN","alpha-3":"BRN","country-code":"096","iso_3166-2":"ISO 3166-2:BN","region":"Asia","sub-region":"South-eastern Asia","intermediate-region":"","region-code":"142","sub-region-code":"035","intermediate-region-code":""},{"name":"Bulgaria","alpha-2":"BG","alpha-3":"BGR","country-code":"100","iso_3166-2":"ISO 3166-2:BG","region":"Europe","sub-region":"Eastern Europe","intermediate-region":"","region-code":"150","sub-region-code":"151","intermediate-region-code":""},{"name":"Burkina Faso","alpha-2":"BF","alpha-3":"BFA","country-code":"854","iso_3166-2":"ISO 3166-2:BF","region":"Africa","sub-region":"Sub-Saharan Africa","intermediate-region":"Western Africa","region-code":"002","sub-region-code":"202","intermediate-region-code":"011"},{"name":"Burundi","alpha-2":"BI","alpha-3":"BDI","country-code":"108","iso_3166-2":"ISO 3166-2:BI","region":"Africa","sub-region":"Sub-Saharan Africa","intermediate-region":"Eastern Africa","region-code":"002","sub-region-code":"202","intermediate-region-code":"014"},{"name":"Cabo Verde","alpha-2":"CV","alpha-3":"CPV","country-code":"132","iso_3166-2":"ISO 3166-2:CV","region":"Africa","sub-region":"Sub-Saharan Africa","intermediate-region":"Western Africa","region-code":"002","sub-region-code":"202","intermediate-region-code":"011"},{"name":"Cambodia","alpha-2":"KH","alpha-3":"KHM","country-code":"116","iso_3166-2":"ISO 3166-2:KH","region":"Asia","sub-region":"South-eastern Asia","intermediate-region":"","region-code":"142","sub-region-code":"035","intermediate-region-code":""},{"name":"Cameroon","alpha-2":"CM","alpha-3":"CMR","country-code":"120","iso_3166-2":"ISO 3166-2:CM","region":"Africa","sub-region":"Sub-Saharan Africa","intermediate-region":"Middle Africa","region-code":"002","sub-region-code":"202","intermediate-region-code":"017"},{"name":"Canada","alpha-2":"CA","alpha-3":"CAN","country-code":"124","iso_3166-2":"ISO 3166-2:CA","region":"Americas","sub-region":"Northern America","intermediate-region":"","region-code":"019","sub-region-code":"021","intermediate-region-code":""},{"name":"Cayman Islands","alpha-2":"KY","alpha-3":"CYM","country-code":"136","iso_3166-2":"ISO 3166-2:KY","region":"Americas","sub-region":"Latin America and the Caribbean","intermediate-region":"Caribbean","region-code":"019","sub-region-code":"419","intermediate-region-code":"029"},{"name":"Central African Republic","alpha-2":"CF","alpha-3":"CAF","country-code":"140","iso_3166-2":"ISO 3166-2:CF","region":"Africa","sub-region":"Sub-Saharan Africa","intermediate-region":"Middle Africa","region-code":"002","sub-region-code":"202","intermediate-region-code":"017"},{"name":"Chad","alpha-2":"TD","alpha-3":"TCD","country-code":"148","iso_3166-2":"ISO 3166-2:TD","region":"Africa","sub-region":"Sub-Saharan Africa","intermediate-region":"Middle Africa","region-code":"002","sub-region-code":"202","intermediate-region-code":"017"},{"name":"Chile","alpha-2":"CL","alpha-3":"CHL","country-code":"152","iso_3166-2":"ISO 3166-2:CL","region":"Americas","sub-region":"Latin America and the Caribbean","intermediate-region":"South America","region-code":"019","sub-region-code":"419","intermediate-region-code":"005"},{"name":"China","alpha-2":"CN","alpha-3":"CHN","country-code":"156","iso_3166-2":"ISO 3166-2:CN","region":"Asia","sub-region":"Eastern Asia","intermediate-region":"","region-code":"142","sub-region-code":"030","intermediate-region-code":""},{"name":"Christmas Island","alpha-2":"CX","alpha-3":"CXR","country-code":"162","iso_3166-2":"ISO 3166-2:CX","region":"Oceania","sub-region":"Australia and New Zealand","intermediate-region":"","region-code":"009","sub-region-code":"053","intermediate-region-code":""},{"name":"Cocos (Keeling) Islands","alpha-2":"CC","alpha-3":"CCK","country-code":"166","iso_3166-2":"ISO 3166-2:CC","region":"Oceania","sub-region":"Australia and New Zealand","intermediate-region":"","region-code":"009","sub-region-code":"053","intermediate-region-code":""},{"name":"Colombia","alpha-2":"CO","alpha-3":"COL","country-code":"170","iso_3166-2":"ISO 3166-2:CO","region":"Americas","sub-region":"Latin America and the Caribbean","intermediate-region":"South America","region-code":"019","sub-region-code":"419","intermediate-region-code":"005"},{"name":"Comoros","alpha-2":"KM","alpha-3":"COM","country-code":"174","iso_3166-2":"ISO 3166-2:KM","region":"Africa","sub-region":"Sub-Saharan Africa","intermediate-region":"Eastern Africa","region-code":"002","sub-region-code":"202","intermediate-region-code":"014"},{"name":"Congo","alpha-2":"CG","alpha-3":"COG","country-code":"178","iso_3166-2":"ISO 3166-2:CG","region":"Africa","sub-region":"Sub-Saharan Africa","intermediate-region":"Middle Africa","region-code":"002","sub-region-code":"202","intermediate-region-code":"017"},{"name":"Congo, Democratic Republic of the","alpha-2":"CD","alpha-3":"COD","country-code":"180","iso_3166-2":"ISO 3166-2:CD","region":"Africa","sub-region":"Sub-Saharan Africa","intermediate-region":"Middle Africa","region-code":"002","sub-region-code":"202","intermediate-region-code":"017"},{"name":"Cook Islands","alpha-2":"CK","alpha-3":"COK","country-code":"184","iso_3166-2":"ISO 3166-2:CK","region":"Oceania","sub-region":"Polynesia","intermediate-region":"","region-code":"009","sub-region-code":"061","intermediate-region-code":""},{"name":"Costa Rica","alpha-2":"CR","alpha-3":"CRI","country-code":"188","iso_3166-2":"ISO 3166-2:CR","region":"Americas","sub-region":"Latin America and the Caribbean","intermediate-region":"Central America","region-code":"019","sub-region-code":"419","intermediate-region-code":"013"},{"name":"Côte d'Ivoire","alpha-2":"CI","alpha-3":"CIV","country-code":"384","iso_3166-2":"ISO 3166-2:CI","region":"Africa","sub-region":"Sub-Saharan Africa","intermediate-region":"Western Africa","region-code":"002","sub-region-code":"202","intermediate-region-code":"011"},{"name":"Croatia","alpha-2":"HR","alpha-3":"HRV","country-code":"191","iso_3166-2":"ISO 3166-2:HR","region":"Europe","sub-region":"Southern Europe","intermediate-region":"","region-code":"150","sub-region-code":"039","intermediate-region-code":""},{"name":"Cuba","alpha-2":"CU","alpha-3":"CUB","country-code":"192","iso_3166-2":"ISO 3166-2:CU","region":"Americas","sub-region":"Latin America and the Caribbean","intermediate-region":"Caribbean","region-code":"019","sub-region-code":"419","intermediate-region-code":"029"},{"name":"Curaçao","alpha-2":"CW","alpha-3":"CUW","country-code":"531","iso_3166-2":"ISO 3166-2:CW","region":"Americas","sub-region":"Latin America and the Caribbean","intermediate-region":"Caribbean","region-code":"019","sub-region-code":"419","intermediate-region-code":"029"},{"name":"Cyprus","alpha-2":"CY","alpha-3":"CYP","country-code":"196","iso_3166-2":"ISO 3166-2:CY","region":"Asia","sub-region":"Western Asia","intermediate-region":"","region-code":"142","sub-region-code":"145","intermediate-region-code":""},{"name":"Czechia","alpha-2":"CZ","alpha-3":"CZE","country-code":"203","iso_3166-2":"ISO 3166-2:CZ","region":"Europe","sub-region":"Eastern Europe","intermediate-region":"","region-code":"150","sub-region-code":"151","intermediate-region-code":""},{"name":"Denmark","alpha-2":"DK","alpha-3":"DNK","country-code":"208","iso_3166-2":"ISO 3166-2:DK","region":"Europe","sub-region":"Northern Europe","intermediate-region":"","region-code":"150","sub-region-code":"154","intermediate-region-code":""},{"name":"Djibouti","alpha-2":"DJ","alpha-3":"DJI","country-code":"262","iso_3166-2":"ISO 3166-2:DJ","region":"Africa","sub-region":"Sub-Saharan Africa","intermediate-region":"Eastern Africa","region-code":"002","sub-region-code":"202","intermediate-region-code":"014"},{"name":"Dominica","alpha-2":"DM","alpha-3":"DMA","country-code":"212","iso_3166-2":"ISO 3166-2:DM","region":"Americas","sub-region":"Latin America and the Caribbean","intermediate-region":"Caribbean","region-code":"019","sub-region-code":"419","intermediate-region-code":"029"},{"name":"Dominican Republic","alpha-2":"DO","alpha-3":"DOM","country-code":"214","iso_3166-2":"ISO 3166-2:DO","region":"Americas","sub-region":"Latin America and the Caribbean","intermediate-region":"Caribbean","region-code":"019","sub-region-code":"419","intermediate-region-code":"029"},{"name":"Ecuador","alpha-2":"EC","alpha-3":"ECU","country-code":"218","iso_3166-2":"ISO 3166-2:EC","region":"Americas","sub-region":"Latin America and the Caribbean","intermediate-region":"South America","region-code":"019","sub-region-code":"419","intermediate-region-code":"005"},{"name":"Egypt","alpha-2":"EG","alpha-3":"EGY","country-code":"818","iso_3166-2":"ISO 3166-2:EG","region":"Africa","sub-region":"Northern Africa","intermediate-region":"","region-code":"002","sub-region-code":"015","intermediate-region-code":""},{"name":"El Salvador","alpha-2":"SV","alpha-3":"SLV","country-code":"222","iso_3166-2":"ISO 3166-2:SV","region":"Americas","sub-region":"Latin America and the Caribbean","intermediate-region":"Central America","region-code":"019","sub-region-code":"419","intermediate-region-code":"013"},{"name":"Equatorial Guinea","alpha-2":"GQ","alpha-3":"GNQ","country-code":"226","iso_3166-2":"ISO 3166-2:GQ","region":"Africa","sub-region":"Sub-Saharan Africa","intermediate-region":"Middle Africa","region-code":"002","sub-region-code":"202","intermediate-region-code":"017"},{"name":"Eritrea","alpha-2":"ER","alpha-3":"ERI","country-code":"232","iso_3166-2":"ISO 3166-2:ER","region":"Africa","sub-region":"Sub-Saharan Africa","intermediate-region":"Eastern Africa","region-code":"002","sub-region-code":"202","intermediate-region-code":"014"},{"name":"Estonia","alpha-2":"EE","alpha-3":"EST","country-code":"233","iso_3166-2":"ISO 3166-2:EE","region":"Europe","sub-region":"Northern Europe","intermediate-region":"","region-code":"150","sub-region-code":"154","intermediate-region-code":""},{"name":"Eswatini","alpha-2":"SZ","alpha-3":"SWZ","country-code":"748","iso_3166-2":"ISO 3166-2:SZ","region":"Africa","sub-region":"Sub-Saharan Africa","intermediate-region":"Southern Africa","region-code":"002","sub-region-code":"202","intermediate-region-code":"018"},{"name":"Ethiopia","alpha-2":"ET","alpha-3":"ETH","country-code":"231","iso_3166-2":"ISO 3166-2:ET","region":"Africa","sub-region":"Sub-Saharan Africa","intermediate-region":"Eastern Africa","region-code":"002","sub-region-code":"202","intermediate-region-code":"014"},{"name":"Falkland Islands (Malvinas)","alpha-2":"FK","alpha-3":"FLK","country-code":"238","iso_3166-2":"ISO 3166-2:FK","region":"Americas","sub-region":"Latin America and the Caribbean","intermediate-region":"South America","region-code":"019","sub-region-code":"419","intermediate-region-code":"005"},{"name":"Faroe Islands","alpha-2":"FO","alpha-3":"FRO","country-code":"234","iso_3166-2":"ISO 3166-2:FO","region":"Europe","sub-region":"Northern Europe","intermediate-region":"","region-code":"150","sub-region-code":"154","intermediate-region-code":""},{"name":"Fiji","alpha-2":"FJ","alpha-3":"FJI","country-code":"242","iso_3166-2":"ISO 3166-2:FJ","region":"Oceania","sub-region":"Melanesia","intermediate-region":"","region-code":"009","sub-region-code":"054","intermediate-region-code":""},{"name":"Finland","alpha-2":"FI","alpha-3":"FIN","country-code":"246","iso_3166-2":"ISO 3166-2:FI","region":"Europe","sub-region":"Northern Europe","intermediate-region":"","region-code":"150","sub-region-code":"154","intermediate-region-code":""},{"name":"France","alpha-2":"FR","alpha-3":"FRA","country-code":"250","iso_3166-2":"ISO 3166-2:FR","region":"Europe","sub-region":"Western Europe","intermediate-region":"","region-code":"150","sub-region-code":"155","intermediate-region-code":""},{"name":"French Guiana","alpha-2":"GF","alpha-3":"GUF","country-code":"254","iso_3166-2":"ISO 3166-2:GF","region":"Americas","sub-region":"Latin America and the Caribbean","intermediate-region":"South America","region-code":"019","sub-region-code":"419","intermediate-region-code":"005"},{"name":"French Polynesia","alpha-2":"PF","alpha-3":"PYF","country-code":"258","iso_3166-2":"ISO 3166-2:PF","region":"Oceania","sub-region":"Polynesia","intermediate-region":"","region-code":"009","sub-region-code":"061","intermediate-region-code":""},{"name":"French Southern Territories","alpha-2":"TF","alpha-3":"ATF","country-code":"260","iso_3166-2":"ISO 3166-2:TF","region":"Africa","sub-region":"Sub-Saharan Africa","intermediate-region":"Eastern Africa","region-code":"002","sub-region-code":"202","intermediate-region-code":"014"},{"name":"Gabon","alpha-2":"GA","alpha-3":"GAB","country-code":"266","iso_3166-2":"ISO 3166-2:GA","region":"Africa","sub-region":"Sub-Saharan Africa","intermediate-region":"Middle Africa","region-code":"002","sub-region-code":"202","intermediate-region-code":"017"},{"name":"Gambia","alpha-2":"GM","alpha-3":"GMB","country-code":"270","iso_3166-2":"ISO 3166-2:GM","region":"Africa","sub-region":"Sub-Saharan Africa","intermediate-region":"Western Africa","region-code":"002","sub-region-code":"202","intermediate-region-code":"011"},{"name":"Georgia","alpha-2":"GE","alpha-3":"GEO","country-code":"268","iso_3166-2":"ISO 3166-2:GE","region":"Asia","sub-region":"Western Asia","intermediate-region":"","region-code":"142","sub-region-code":"145","intermediate-region-code":""},{"name":"Germany","alpha-2":"DE","alpha-3":"DEU","country-code":"276","iso_3166-2":"ISO 3166-2:DE","region":"Europe","sub-region":"Western Europe","intermediate-region":"","region-code":"150","sub-region-code":"155","intermediate-region-code":""},{"name":"Ghana","alpha-2":"GH","alpha-3":"GHA","country-code":"288","iso_3166-2":"ISO 3166-2:GH","region":"Africa","sub-region":"Sub-Saharan Africa","intermediate-region":"Western Africa","region-code":"002","sub-region-code":"202","intermediate-region-code":"011"},{"name":"Gibraltar","alpha-2":"GI","alpha-3":"GIB","country-code":"292","iso_3166-2":"ISO 3166-2:GI","region":"Europe","sub-region":"Southern Europe","intermediate-region":"","region-code":"150","sub-region-code":"039","intermediate-region-code":""},{"name":"Greece","alpha-2":"GR","alpha-3":"GRC","country-code":"300","iso_3166-2":"ISO 3166-2:GR","region":"Europe","sub-region":"Southern Europe","intermediate-region":"","region-code":"150","sub-region-code":"039","intermediate-region-code":""},{"name":"Greenland","alpha-2":"GL","alpha-3":"GRL","country-code":"304","iso_3166-2":"ISO 3166-2:GL","region":"Americas","sub-region":"Northern America","intermediate-region":"","region-code":"019","sub-region-code":"021","intermediate-region-code":""},{"name":"Grenada","alpha-2":"GD","alpha-3":"GRD","country-code":"308","iso_3166-2":"ISO 3166-2:GD","region":"Americas","sub-region":"Latin America and the Caribbean","intermediate-region":"Caribbean","region-code":"019","sub-region-code":"419","intermediate-region-code":"029"},{"name":"Guadeloupe","alpha-2":"GP","alpha-3":"GLP","country-code":"312","iso_3166-2":"ISO 3166-2:GP","region":"Americas","sub-region":"Latin America and the Caribbean","intermediate-region":"Caribbean","region-code":"019","sub-region-code":"419","intermediate-region-code":"029"},{"name":"Guam","alpha-2":"GU","alpha-3":"GUM","country-code":"316","iso_3166-2":"ISO 3166-2:GU","region":"Oceania","sub-region":"Micronesia","intermediate-region":"","region-code":"009","sub-region-code":"057","intermediate-region-code":""},{"name":"Guatemala","alpha-2":"GT","alpha-3":"GTM","country-code":"320","iso_3166-2":"ISO 3166-2:GT","region":"Americas","sub-region":"Latin America and the Caribbean","intermediate-region":"Central America","region-code":"019","sub-region-code":"419","intermediate-region-code":"013"},{"name":"Guernsey","alpha-2":"GG","alpha-3":"GGY","country-code":"831","iso_3166-2":"ISO 3166-2:GG","region":"Europe","sub-region":"Northern Europe","intermediate-region":"Channel Islands","region-code":"150","sub-region-code":"154","intermediate-region-code":"830"},{"name":"Guinea","alpha-2":"GN","alpha-3":"GIN","country-code":"324","iso_3166-2":"ISO 3166-2:GN","region":"Africa","sub-region":"Sub-Saharan Africa","intermediate-region":"Western Africa","region-code":"002","sub-region-code":"202","intermediate-region-code":"011"},{"name":"Guinea-Bissau","alpha-2":"GW","alpha-3":"GNB","country-code":"624","iso_3166-2":"ISO 3166-2:GW","region":"Africa","sub-region":"Sub-Saharan Africa","intermediate-region":"Western Africa","region-code":"002","sub-region-code":"202","intermediate-region-code":"011"},{"name":"Guyana","alpha-2":"GY","alpha-3":"GUY","country-code":"328","iso_3166-2":"ISO 3166-2:GY","region":"Americas","sub-region":"Latin America and the Caribbean","intermediate-region":"South America","region-code":"019","sub-region-code":"419","intermediate-region-code":"005"},{"name":"Haiti","alpha-2":"HT","alpha-3":"HTI","country-code":"332","iso_3166-2":"ISO 3166-2:HT","region":"Americas","sub-region":"Latin America and the Caribbean","intermediate-region":"Caribbean","region-code":"019","sub-region-code":"419","intermediate-region-code":"029"},{"name":"Heard Island and McDonald Islands","alpha-2":"HM","alpha-3":"HMD","country-code":"334","iso_3166-2":"ISO 3166-2:HM","region":"Oceania","sub-region":"Australia and New Zealand","intermediate-region":"","region-code":"009","sub-region-code":"053","intermediate-region-code":""},{"name":"Holy See","alpha-2":"VA","alpha-3":"VAT","country-code":"336","iso_3166-2":"ISO 3166-2:VA","region":"Europe","sub-region":"Southern Europe","intermediate-region":"","region-code":"150","sub-region-code":"039","intermediate-region-code":""},{"name":"Honduras","alpha-2":"HN","alpha-3":"HND","country-code":"340","iso_3166-2":"ISO 3166-2:HN","region":"Americas","sub-region":"Latin America and the Caribbean","intermediate-region":"Central America","region-code":"019","sub-region-code":"419","intermediate-region-code":"013"},{"name":"Hong Kong","alpha-2":"HK","alpha-3":"HKG","country-code":"344","iso_3166-2":"ISO 3166-2:HK","region":"Asia","sub-region":"Eastern Asia","intermediate-region":"","region-code":"142","sub-region-code":"030","intermediate-region-code":""},{"name":"Hungary","alpha-2":"HU","alpha-3":"HUN","country-code":"348","iso_3166-2":"ISO 3166-2:HU","region":"Europe","sub-region":"Eastern Europe","intermediate-region":"","region-code":"150","sub-region-code":"151","intermediate-region-code":""},{"name":"Iceland","alpha-2":"IS","alpha-3":"ISL","country-code":"352","iso_3166-2":"ISO 3166-2:IS","region":"Europe","sub-region":"Northern Europe","intermediate-region":"","region-code":"150","sub-region-code":"154","intermediate-region-code":""},{"name":"India","alpha-2":"IN","alpha-3":"IND","country-code":"356","iso_3166-2":"ISO 3166-2:IN","region":"Asia","sub-region":"Southern Asia","intermediate-region":"","region-code":"142","sub-region-code":"034","intermediate-region-code":""},{"name":"Indonesia","alpha-2":"ID","alpha-3":"IDN","country-code":"360","iso_3166-2":"ISO 3166-2:ID","region":"Asia","sub-region":"South-eastern Asia","intermediate-region":"","region-code":"142","sub-region-code":"035","intermediate-region-code":""},{"name":"Iran (Islamic Republic of)","alpha-2":"IR","alpha-3":"IRN","country-code":"364","iso_3166-2":"ISO 3166-2:IR","region":"Asia","sub-region":"Southern Asia","intermediate-region":"","region-code":"142","sub-region-code":"034","intermediate-region-code":""},{"name":"Iraq","alpha-2":"IQ","alpha-3":"IRQ","country-code":"368","iso_3166-2":"ISO 3166-2:IQ","region":"Asia","sub-region":"Western Asia","intermediate-region":"","region-code":"142","sub-region-code":"145","intermediate-region-code":""},{"name":"Ireland","alpha-2":"IE","alpha-3":"IRL","country-code":"372","iso_3166-2":"ISO 3166-2:IE","region":"Europe","sub-region":"Northern Europe","intermediate-region":"","region-code":"150","sub-region-code":"154","intermediate-region-code":""},{"name":"Isle of Man","alpha-2":"IM","alpha-3":"IMN","country-code":"833","iso_3166-2":"ISO 3166-2:IM","region":"Europe","sub-region":"Northern Europe","intermediate-region":"","region-code":"150","sub-region-code":"154","intermediate-region-code":""},{"name":"Israel","alpha-2":"IL","alpha-3":"ISR","country-code":"376","iso_3166-2":"ISO 3166-2:IL","region":"Asia","sub-region":"Western Asia","intermediate-region":"","region-code":"142","sub-region-code":"145","intermediate-region-code":""},{"name":"Italy","alpha-2":"IT","alpha-3":"ITA","country-code":"380","iso_3166-2":"ISO 3166-2:IT","region":"Europe","sub-region":"Southern Europe","intermediate-region":"","region-code":"150","sub-region-code":"039","intermediate-region-code":""},{"name":"Jamaica","alpha-2":"JM","alpha-3":"JAM","country-code":"388","iso_3166-2":"ISO 3166-2:JM","region":"Americas","sub-region":"Latin America and the Caribbean","intermediate-region":"Caribbean","region-code":"019","sub-region-code":"419","intermediate-region-code":"029"},{"name":"Japan","alpha-2":"JP","alpha-3":"JPN","country-code":"392","iso_3166-2":"ISO 3166-2:JP","region":"Asia","sub-region":"Eastern Asia","intermediate-region":"","region-code":"142","sub-region-code":"030","intermediate-region-code":""},{"name":"Jersey","alpha-2":"JE","alpha-3":"JEY","country-code":"832","iso_3166-2":"ISO 3166-2:JE","region":"Europe","sub-region":"Northern Europe","intermediate-region":"Channel Islands","region-code":"150","sub-region-code":"154","intermediate-region-code":"830"},{"name":"Jordan","alpha-2":"JO","alpha-3":"JOR","country-code":"400","iso_3166-2":"ISO 3166-2:JO","region":"Asia","sub-region":"Western Asia","intermediate-region":"","region-code":"142","sub-region-code":"145","intermediate-region-code":""},{"name":"Kazakhstan","alpha-2":"KZ","alpha-3":"KAZ","country-code":"398","iso_3166-2":"ISO 3166-2:KZ","region":"Asia","sub-region":"Central Asia","intermediate-region":"","region-code":"142","sub-region-code":"143","intermediate-region-code":""},{"name":"Kenya","alpha-2":"KE","alpha-3":"KEN","country-code":"404","iso_3166-2":"ISO 3166-2:KE","region":"Africa","sub-region":"Sub-Saharan Africa","intermediate-region":"Eastern Africa","region-code":"002","sub-region-code":"202","intermediate-region-code":"014"},{"name":"Kiribati","alpha-2":"KI","alpha-3":"KIR","country-code":"296","iso_3166-2":"ISO 3166-2:KI","region":"Oceania","sub-region":"Micronesia","intermediate-region":"","region-code":"009","sub-region-code":"057","intermediate-region-code":""},{"name":"Korea (Democratic People's Republic of)","alpha-2":"KP","alpha-3":"PRK","country-code":"408","iso_3166-2":"ISO 3166-2:KP","region":"Asia","sub-region":"Eastern Asia","intermediate-region":"","region-code":"142","sub-region-code":"030","intermediate-region-code":""},{"name":"Korea, Republic of","alpha-2":"KR","alpha-3":"KOR","country-code":"410","iso_3166-2":"ISO 3166-2:KR","region":"Asia","sub-region":"Eastern Asia","intermediate-region":"","region-code":"142","sub-region-code":"030","intermediate-region-code":""},{"name":"Kuwait","alpha-2":"KW","alpha-3":"KWT","country-code":"414","iso_3166-2":"ISO 3166-2:KW","region":"Asia","sub-region":"Western Asia","intermediate-region":"","region-code":"142","sub-region-code":"145","intermediate-region-code":""},{"name":"Kyrgyzstan","alpha-2":"KG","alpha-3":"KGZ","country-code":"417","iso_3166-2":"ISO 3166-2:KG","region":"Asia","sub-region":"Central Asia","intermediate-region":"","region-code":"142","sub-region-code":"143","intermediate-region-code":""},{"name":"Lao People's Democratic Republic","alpha-2":"LA","alpha-3":"LAO","country-code":"418","iso_3166-2":"ISO 3166-2:LA","region":"Asia","sub-region":"South-eastern Asia","intermediate-region":"","region-code":"142","sub-region-code":"035","intermediate-region-code":""},{"name":"Latvia","alpha-2":"LV","alpha-3":"LVA","country-code":"428","iso_3166-2":"ISO 3166-2:LV","region":"Europe","sub-region":"Northern Europe","intermediate-region":"","region-code":"150","sub-region-code":"154","intermediate-region-code":""},{"name":"Lebanon","alpha-2":"LB","alpha-3":"LBN","country-code":"422","iso_3166-2":"ISO 3166-2:LB","region":"Asia","sub-region":"Western Asia","intermediate-region":"","region-code":"142","sub-region-code":"145","intermediate-region-code":""},{"name":"Lesotho","alpha-2":"LS","alpha-3":"LSO","country-code":"426","iso_3166-2":"ISO 3166-2:LS","region":"Africa","sub-region":"Sub-Saharan Africa","intermediate-region":"Southern Africa","region-code":"002","sub-region-code":"202","intermediate-region-code":"018"},{"name":"Liberia","alpha-2":"LR","alpha-3":"LBR","country-code":"430","iso_3166-2":"ISO 3166-2:LR","region":"Africa","sub-region":"Sub-Saharan Africa","intermediate-region":"Western Africa","region-code":"002","sub-region-code":"202","intermediate-region-code":"011"},{"name":"Libya","alpha-2":"LY","alpha-3":"LBY","country-code":"434","iso_3166-2":"ISO 3166-2:LY","region":"Africa","sub-region":"Northern Africa","intermediate-region":"","region-code":"002","sub-region-code":"015","intermediate-region-code":""},{"name":"Liechtenstein","alpha-2":"LI","alpha-3":"LIE","country-code":"438","iso_3166-2":"ISO 3166-2:LI","region":"Europe","sub-region":"Western Europe","intermediate-region":"","region-code":"150","sub-region-code":"155","intermediate-region-code":""},{"name":"Lithuania","alpha-2":"LT","alpha-3":"LTU","country-code":"440","iso_3166-2":"ISO 3166-2:LT","region":"Europe","sub-region":"Northern Europe","intermediate-region":"","region-code":"150","sub-region-code":"154","intermediate-region-code":""},{"name":"Luxembourg","alpha-2":"LU","alpha-3":"LUX","country-code":"442","iso_3166-2":"ISO 3166-2:LU","region":"Europe","sub-region":"Western Europe","intermediate-region":"","region-code":"150","sub-region-code":"155","intermediate-region-code":""},{"name":"Macao","alpha-2":"MO","alpha-3":"MAC","country-code":"446","iso_3166-2":"ISO 3166-2:MO","region":"Asia","sub-region":"Eastern Asia","intermediate-region":"","region-code":"142","sub-region-code":"030","intermediate-region-code":""},{"name":"Madagascar","alpha-2":"MG","alpha-3":"MDG","country-code":"450","iso_3166-2":"ISO 3166-2:MG","region":"Africa","sub-region":"Sub-Saharan Africa","intermediate-region":"Eastern Africa","region-code":"002","sub-region-code":"202","intermediate-region-code":"014"},{"name":"Malawi","alpha-2":"MW","alpha-3":"MWI","country-code":"454","iso_3166-2":"ISO 3166-2:MW","region":"Africa","sub-region":"Sub-Saharan Africa","intermediate-region":"Eastern Africa","region-code":"002","sub-region-code":"202","intermediate-region-code":"014"},{"name":"Malaysia","alpha-2":"MY","alpha-3":"MYS","country-code":"458","iso_3166-2":"ISO 3166-2:MY","region":"Asia","sub-region":"South-eastern Asia","intermediate-region":"","region-code":"142","sub-region-code":"035","intermediate-region-code":""},{"name":"Maldives","alpha-2":"MV","alpha-3":"MDV","country-code":"462","iso_3166-2":"ISO 3166-2:MV","region":"Asia","sub-region":"Southern Asia","intermediate-region":"","region-code":"142","sub-region-code":"034","intermediate-region-code":""},{"name":"Mali","alpha-2":"ML","alpha-3":"MLI","country-code":"466","iso_3166-2":"ISO 3166-2:ML","region":"Africa","sub-region":"Sub-Saharan Africa","intermediate-region":"Western Africa","region-code":"002","sub-region-code":"202","intermediate-region-code":"011"},{"name":"Malta","alpha-2":"MT","alpha-3":"MLT","country-code":"470","iso_3166-2":"ISO 3166-2:MT","region":"Europe","sub-region":"Southern Europe","intermediate-region":"","region-code":"150","sub-region-code":"039","intermediate-region-code":""},{"name":"Marshall Islands","alpha-2":"MH","alpha-3":"MHL","country-code":"584","iso_3166-2":"ISO 3166-2:MH","region":"Oceania","sub-region":"Micronesia","intermediate-region":"","region-code":"009","sub-region-code":"057","intermediate-region-code":""},{"name":"Martinique","alpha-2":"MQ","alpha-3":"MTQ","country-code":"474","iso_3166-2":"ISO 3166-2:MQ","region":"Americas","sub-region":"Latin America and the Caribbean","intermediate-region":"Caribbean","region-code":"019","sub-region-code":"419","intermediate-region-code":"029"},{"name":"Mauritania","alpha-2":"MR","alpha-3":"MRT","country-code":"478","iso_3166-2":"ISO 3166-2:MR","region":"Africa","sub-region":"Sub-Saharan Africa","intermediate-region":"Western Africa","region-code":"002","sub-region-code":"202","intermediate-region-code":"011"},{"name":"Mauritius","alpha-2":"MU","alpha-3":"MUS","country-code":"480","iso_3166-2":"ISO 3166-2:MU","region":"Africa","sub-region":"Sub-Saharan Africa","intermediate-region":"Eastern Africa","region-code":"002","sub-region-code":"202","intermediate-region-code":"014"},{"name":"Mayotte","alpha-2":"YT","alpha-3":"MYT","country-code":"175","iso_3166-2":"ISO 3166-2:YT","region":"Africa","sub-region":"Sub-Saharan Africa","intermediate-region":"Eastern Africa","region-code":"002","sub-region-code":"202","intermediate-region-code":"014"},{"name":"Mexico","alpha-2":"MX","alpha-3":"MEX","country-code":"484","iso_3166-2":"ISO 3166-2:MX","region":"Americas","sub-region":"Latin America and the Caribbean","intermediate-region":"Central America","region-code":"019","sub-region-code":"419","intermediate-region-code":"013"},{"name":"Micronesia (Federated States of)","alpha-2":"FM","alpha-3":"FSM","country-code":"583","iso_3166-2":"ISO 3166-2:FM","region":"Oceania","sub-region":"Micronesia","intermediate-region":"","region-code":"009","sub-region-code":"057","intermediate-region-code":""},{"name":"Moldova, Republic of","alpha-2":"MD","alpha-3":"MDA","country-code":"498","iso_3166-2":"ISO 3166-2:MD","region":"Europe","sub-region":"Eastern Europe","intermediate-region":"","region-code":"150","sub-region-code":"151","intermediate-region-code":""},{"name":"Monaco","alpha-2":"MC","alpha-3":"MCO","country-code":"492","iso_3166-2":"ISO 3166-2:MC","region":"Europe","sub-region":"Western Europe","intermediate-region":"","region-code":"150","sub-region-code":"155","intermediate-region-code":""},{"name":"Mongolia","alpha-2":"MN","alpha-3":"MNG","country-code":"496","iso_3166-2":"ISO 3166-2:MN","region":"Asia","sub-region":"Eastern Asia","intermediate-region":"","region-code":"142","sub-region-code":"030","intermediate-region-code":""},{"name":"Montenegro","alpha-2":"ME","alpha-3":"MNE","country-code":"499","iso_3166-2":"ISO 3166-2:ME","region":"Europe","sub-region":"Southern Europe","intermediate-region":"","region-code":"150","sub-region-code":"039","intermediate-region-code":""},{"name":"Montserrat","alpha-2":"MS","alpha-3":"MSR","country-code":"500","iso_3166-2":"ISO 3166-2:MS","region":"Americas","sub-region":"Latin America and the Caribbean","intermediate-region":"Caribbean","region-code":"019","sub-region-code":"419","intermediate-region-code":"029"},{"name":"Morocco","alpha-2":"MA","alpha-3":"MAR","country-code":"504","iso_3166-2":"ISO 3166-2:MA","region":"Africa","sub-region":"Northern Africa","intermediate-region":"","region-code":"002","sub-region-code":"015","intermediate-region-code":""},{"name":"Mozambique","alpha-2":"MZ","alpha-3":"MOZ","country-code":"508","iso_3166-2":"ISO 3166-2:MZ","region":"Africa","sub-region":"Sub-Saharan Africa","intermediate-region":"Eastern Africa","region-code":"002","sub-region-code":"202","intermediate-region-code":"014"},{"name":"Myanmar","alpha-2":"MM","alpha-3":"MMR","country-code":"104","iso_3166-2":"ISO 3166-2:MM","region":"Asia","sub-region":"South-eastern Asia","intermediate-region":"","region-code":"142","sub-region-code":"035","intermediate-region-code":""},{"name":"Namibia","alpha-2":"NA","alpha-3":"NAM","country-code":"516","iso_3166-2":"ISO 3166-2:NA","region":"Africa","sub-region":"Sub-Saharan Africa","intermediate-region":"Southern Africa","region-code":"002","sub-region-code":"202","intermediate-region-code":"018"},{"name":"Nauru","alpha-2":"NR","alpha-3":"NRU","country-code":"520","iso_3166-2":"ISO 3166-2:NR","region":"Oceania","sub-region":"Micronesia","intermediate-region":"","region-code":"009","sub-region-code":"057","intermediate-region-code":""},{"name":"Nepal","alpha-2":"NP","alpha-3":"NPL","country-code":"524","iso_3166-2":"ISO 3166-2:NP","region":"Asia","sub-region":"Southern Asia","intermediate-region":"","region-code":"142","sub-region-code":"034","intermediate-region-code":""},{"name":"Netherlands","alpha-2":"NL","alpha-3":"NLD","country-code":"528","iso_3166-2":"ISO 3166-2:NL","region":"Europe","sub-region":"Western Europe","intermediate-region":"","region-code":"150","sub-region-code":"155","intermediate-region-code":""},{"name":"New Caledonia","alpha-2":"NC","alpha-3":"NCL","country-code":"540","iso_3166-2":"ISO 3166-2:NC","region":"Oceania","sub-region":"Melanesia","intermediate-region":"","region-code":"009","sub-region-code":"054","intermediate-region-code":""},{"name":"New Zealand","alpha-2":"NZ","alpha-3":"NZL","country-code":"554","iso_3166-2":"ISO 3166-2:NZ","region":"Oceania","sub-region":"Australia and New Zealand","intermediate-region":"","region-code":"009","sub-region-code":"053","intermediate-region-code":""},{"name":"Nicaragua","alpha-2":"NI","alpha-3":"NIC","country-code":"558","iso_3166-2":"ISO 3166-2:NI","region":"Americas","sub-region":"Latin America and the Caribbean","intermediate-region":"Central America","region-code":"019","sub-region-code":"419","intermediate-region-code":"013"},{"name":"Niger","alpha-2":"NE","alpha-3":"NER","country-code":"562","iso_3166-2":"ISO 3166-2:NE","region":"Africa","sub-region":"Sub-Saharan Africa","intermediate-region":"Western Africa","region-code":"002","sub-region-code":"202","intermediate-region-code":"011"},{"name":"Nigeria","alpha-2":"NG","alpha-3":"NGA","country-code":"566","iso_3166-2":"ISO 3166-2:NG","region":"Africa","sub-region":"Sub-Saharan Africa","intermediate-region":"Western Africa","region-code":"002","sub-region-code":"202","intermediate-region-code":"011"},{"name":"Niue","alpha-2":"NU","alpha-3":"NIU","country-code":"570","iso_3166-2":"ISO 3166-2:NU","region":"Oceania","sub-region":"Polynesia","intermediate-region":"","region-code":"009","sub-region-code":"061","intermediate-region-code":""},{"name":"Norfolk Island","alpha-2":"NF","alpha-3":"NFK","country-code":"574","iso_3166-2":"ISO 3166-2:NF","region":"Oceania","sub-region":"Australia and New Zealand","intermediate-region":"","region-code":"009","sub-region-code":"053","intermediate-region-code":""},{"name":"North Macedonia","alpha-2":"MK","alpha-3":"MKD","country-code":"807","iso_3166-2":"ISO 3166-2:MK","region":"Europe","sub-region":"Southern Europe","intermediate-region":"","region-code":"150","sub-region-code":"039","intermediate-region-code":""},{"name":"Northern Mariana Islands","alpha-2":"MP","alpha-3":"MNP","country-code":"580","iso_3166-2":"ISO 3166-2:MP","region":"Oceania","sub-region":"Micronesia","intermediate-region":"","region-code":"009","sub-region-code":"057","intermediate-region-code":""},{"name":"Norway","alpha-2":"NO","alpha-3":"NOR","country-code":"578","iso_3166-2":"ISO 3166-2:NO","region":"Europe","sub-region":"Northern Europe","intermediate-region":"","region-code":"150","sub-region-code":"154","intermediate-region-code":""},{"name":"Oman","alpha-2":"OM","alpha-3":"OMN","country-code":"512","iso_3166-2":"ISO 3166-2:OM","region":"Asia","sub-region":"Western Asia","intermediate-region":"","region-code":"142","sub-region-code":"145","intermediate-region-code":""},{"name":"Pakistan","alpha-2":"PK","alpha-3":"PAK","country-code":"586","iso_3166-2":"ISO 3166-2:PK","region":"Asia","sub-region":"Southern Asia","intermediate-region":"","region-code":"142","sub-region-code":"034","intermediate-region-code":""},{"name":"Palau","alpha-2":"PW","alpha-3":"PLW","country-code":"585","iso_3166-2":"ISO 3166-2:PW","region":"Oceania","sub-region":"Micronesia","intermediate-region":"","region-code":"009","sub-region-code":"057","intermediate-region-code":""},{"name":"Palestine, State of","alpha-2":"PS","alpha-3":"PSE","country-code":"275","iso_3166-2":"ISO 3166-2:PS","region":"Asia","sub-region":"Western Asia","intermediate-region":"","region-code":"142","sub-region-code":"145","intermediate-region-code":""},{"name":"Panama","alpha-2":"PA","alpha-3":"PAN","country-code":"591","iso_3166-2":"ISO 3166-2:PA","region":"Americas","sub-region":"Latin America and the Caribbean","intermediate-region":"Central America","region-code":"019","sub-region-code":"419","intermediate-region-code":"013"},{"name":"Papua New Guinea","alpha-2":"PG","alpha-3":"PNG","country-code":"598","iso_3166-2":"ISO 3166-2:PG","region":"Oceania","sub-region":"Melanesia","intermediate-region":"","region-code":"009","sub-region-code":"054","intermediate-region-code":""},{"name":"Paraguay","alpha-2":"PY","alpha-3":"PRY","country-code":"600","iso_3166-2":"ISO 3166-2:PY","region":"Americas","sub-region":"Latin America and the Caribbean","intermediate-region":"South America","region-code":"019","sub-region-code":"419","intermediate-region-code":"005"},{"name":"Peru","alpha-2":"PE","alpha-3":"PER","country-code":"604","iso_3166-2":"ISO 3166-2:PE","region":"Americas","sub-region":"Latin America and the Caribbean","intermediate-region":"South America","region-code":"019","sub-region-code":"419","intermediate-region-code":"005"},{"name":"Philippines","alpha-2":"PH","alpha-3":"PHL","country-code":"608","iso_3166-2":"ISO 3166-2:PH","region":"Asia","sub-region":"South-eastern Asia","intermediate-region":"","region-code":"142","sub-region-code":"035","intermediate-region-code":""},{"name":"Pitcairn","alpha-2":"PN","alpha-3":"PCN","country-code":"612","iso_3166-2":"ISO 3166-2:PN","region":"Oceania","sub-region":"Polynesia","intermediate-region":"","region-code":"009","sub-region-code":"061","intermediate-region-code":""},{"name":"Poland","alpha-2":"PL","alpha-3":"POL","country-code":"616","iso_3166-2":"ISO 3166-2:PL","region":"Europe","sub-region":"Eastern Europe","intermediate-region":"","region-code":"150","sub-region-code":"151","intermediate-region-code":""},{"name":"Portugal","alpha-2":"PT","alpha-3":"PRT","country-code":"620","iso_3166-2":"ISO 3166-2:PT","region":"Europe","sub-region":"Southern Europe","intermediate-region":"","region-code":"150","sub-region-code":"039","intermediate-region-code":""},{"name":"Puerto Rico","alpha-2":"PR","alpha-3":"PRI","country-code":"630","iso_3166-2":"ISO 3166-2:PR","region":"Americas","sub-region":"Latin America and the Caribbean","intermediate-region":"Caribbean","region-code":"019","sub-region-code":"419","intermediate-region-code":"029"},{"name":"Qatar","alpha-2":"QA","alpha-3":"QAT","country-code":"634","iso_3166-2":"ISO 3166-2:QA","region":"Asia","sub-region":"Western Asia","intermediate-region":"","region-code":"142","sub-region-code":"145","intermediate-region-code":""},{"name":"Réunion","alpha-2":"RE","alpha-3":"REU","country-code":"638","iso_3166-2":"ISO 3166-2:RE","region":"Africa","sub-region":"Sub-Saharan Africa","intermediate-region":"Eastern Africa","region-code":"002","sub-region-code":"202","intermediate-region-code":"014"},{"name":"Romania","alpha-2":"RO","alpha-3":"ROU","country-code":"642","iso_3166-2":"ISO 3166-2:RO","region":"Europe","sub-region":"Eastern Europe","intermediate-region":"","region-code":"150","sub-region-code":"151","intermediate-region-code":""},{"name":"Russian Federation","alpha-2":"RU","alpha-3":"RUS","country-code":"643","iso_3166-2":"ISO 3166-2:RU","region":"Europe","sub-region":"Eastern Europe","intermediate-region":"","region-code":"150","sub-region-code":"151","intermediate-region-code":""},{"name":"Rwanda","alpha-2":"RW","alpha-3":"RWA","country-code":"646","iso_3166-2":"ISO 3166-2:RW","region":"Africa","sub-region":"Sub-Saharan Africa","intermediate-region":"Eastern Africa","region-code":"002","sub-region-code":"202","intermediate-region-code":"014"},{"name":"Saint Barthélemy","alpha-2":"BL","alpha-3":"BLM","country-code":"652","iso_3166-2":"ISO 3166-2:BL","region":"Americas","sub-region":"Latin America and the Caribbean","intermediate-region":"Caribbean","region-code":"019","sub-region-code":"419","intermediate-region-code":"029"},{"name":"Saint Helena, Ascension and Tristan da Cunha","alpha-2":"SH","alpha-3":"SHN","country-code":"654","iso_3166-2":"ISO 3166-2:SH","region":"Africa","sub-region":"Sub-Saharan Africa","intermediate-region":"Western Africa","region-code":"002","sub-region-code":"202","intermediate-region-code":"011"},{"name":"Saint Kitts and Nevis","alpha-2":"KN","alpha-3":"KNA","country-code":"659","iso_3166-2":"ISO 3166-2:KN","region":"Americas","sub-region":"Latin America and the Caribbean","intermediate-region":"Caribbean","region-code":"019","sub-region-code":"419","intermediate-region-code":"029"},{"name":"Saint Lucia","alpha-2":"LC","alpha-3":"LCA","country-code":"662","iso_3166-2":"ISO 3166-2:LC","region":"Americas","sub-region":"Latin America and the Caribbean","intermediate-region":"Caribbean","region-code":"019","sub-region-code":"419","intermediate-region-code":"029"},{"name":"Saint Martin (French part)","alpha-2":"MF","alpha-3":"MAF","country-code":"663","iso_3166-2":"ISO 3166-2:MF","region":"Americas","sub-region":"Latin America and the Caribbean","intermediate-region":"Caribbean","region-code":"019","sub-region-code":"419","intermediate-region-code":"029"},{"name":"Saint Pierre and Miquelon","alpha-2":"PM","alpha-3":"SPM","country-code":"666","iso_3166-2":"ISO 3166-2:PM","region":"Americas","sub-region":"Northern America","intermediate-region":"","region-code":"019","sub-region-code":"021","intermediate-region-code":""},{"name":"Saint Vincent and the Grenadines","alpha-2":"VC","alpha-3":"VCT","country-code":"670","iso_3166-2":"ISO 3166-2:VC","region":"Americas","sub-region":"Latin America and the Caribbean","intermediate-region":"Caribbean","region-code":"019","sub-region-code":"419","intermediate-region-code":"029"},{"name":"Samoa","alpha-2":"WS","alpha-3":"WSM","country-code":"882","iso_3166-2":"ISO 3166-2:WS","region":"Oceania","sub-region":"Polynesia","intermediate-region":"","region-code":"009","sub-region-code":"061","intermediate-region-code":""},{"name":"San Marino","alpha-2":"SM","alpha-3":"SMR","country-code":"674","iso_3166-2":"ISO 3166-2:SM","region":"Europe","sub-region":"Southern Europe","intermediate-region":"","region-code":"150","sub-region-code":"039","intermediate-region-code":""},{"name":"Sao Tome and Principe","alpha-2":"ST","alpha-3":"STP","country-code":"678","iso_3166-2":"ISO 3166-2:ST","region":"Africa","sub-region":"Sub-Saharan Africa","intermediate-region":"Middle Africa","region-code":"002","sub-region-code":"202","intermediate-region-code":"017"},{"name":"Saudi Arabia","alpha-2":"SA","alpha-3":"SAU","country-code":"682","iso_3166-2":"ISO 3166-2:SA","region":"Asia","sub-region":"Western Asia","intermediate-region":"","region-code":"142","sub-region-code":"145","intermediate-region-code":""},{"name":"Senegal","alpha-2":"SN","alpha-3":"SEN","country-code":"686","iso_3166-2":"ISO 3166-2:SN","region":"Africa","sub-region":"Sub-Saharan Africa","intermediate-region":"Western Africa","region-code":"002","sub-region-code":"202","intermediate-region-code":"011"},{"name":"Serbia","alpha-2":"RS","alpha-3":"SRB","country-code":"688","iso_3166-2":"ISO 3166-2:RS","region":"Europe","sub-region":"Southern Europe","intermediate-region":"","region-code":"150","sub-region-code":"039","intermediate-region-code":""},{"name":"Seychelles","alpha-2":"SC","alpha-3":"SYC","country-code":"690","iso_3166-2":"ISO 3166-2:SC","region":"Africa","sub-region":"Sub-Saharan Africa","intermediate-region":"Eastern Africa","region-code":"002","sub-region-code":"202","intermediate-region-code":"014"},{"name":"Sierra Leone","alpha-2":"SL","alpha-3":"SLE","country-code":"694","iso_3166-2":"ISO 3166-2:SL","region":"Africa","sub-region":"Sub-Saharan Africa","intermediate-region":"Western Africa","region-code":"002","sub-region-code":"202","intermediate-region-code":"011"},{"name":"Singapore","alpha-2":"SG","alpha-3":"SGP","country-code":"702","iso_3166-2":"ISO 3166-2:SG","region":"Asia","sub-region":"South-eastern Asia","intermediate-region":"","region-code":"142","sub-region-code":"035","intermediate-region-code":""},{"name":"Sint Maarten (Dutch part)","alpha-2":"SX","alpha-3":"SXM","country-code":"534","iso_3166-2":"ISO 3166-2:SX","region":"Americas","sub-region":"Latin America and the Caribbean","intermediate-region":"Caribbean","region-code":"019","sub-region-code":"419","intermediate-region-code":"029"},{"name":"Slovakia","alpha-2":"SK","alpha-3":"SVK","country-code":"703","iso_3166-2":"ISO 3166-2:SK","region":"Europe","sub-region":"Eastern Europe","intermediate-region":"","region-code":"150","sub-region-code":"151","intermediate-region-code":""},{"name":"Slovenia","alpha-2":"SI","alpha-3":"SVN","country-code":"705","iso_3166-2":"ISO 3166-2:SI","region":"Europe","sub-region":"Southern Europe","intermediate-region":"","region-code":"150","sub-region-code":"039","intermediate-region-code":""},{"name":"Solomon Islands","alpha-2":"SB","alpha-3":"SLB","country-code":"090","iso_3166-2":"ISO 3166-2:SB","region":"Oceania","sub-region":"Melanesia","intermediate-region":"","region-code":"009","sub-region-code":"054","intermediate-region-code":""},{"name":"Somalia","alpha-2":"SO","alpha-3":"SOM","country-code":"706","iso_3166-2":"ISO 3166-2:SO","region":"Africa","sub-region":"Sub-Saharan Africa","intermediate-region":"Eastern Africa","region-code":"002","sub-region-code":"202","intermediate-region-code":"014"},{"name":"South Africa","alpha-2":"ZA","alpha-3":"ZAF","country-code":"710","iso_3166-2":"ISO 3166-2:ZA","region":"Africa","sub-region":"Sub-Saharan Africa","intermediate-region":"Southern Africa","region-code":"002","sub-region-code":"202","intermediate-region-code":"018"},{"name":"South Georgia and the South Sandwich Islands","alpha-2":"GS","alpha-3":"SGS","country-code":"239","iso_3166-2":"ISO 3166-2:GS","region":"Americas","sub-region":"Latin America and the Caribbean","intermediate-region":"South America","region-code":"019","sub-region-code":"419","intermediate-region-code":"005"},{"name":"South Sudan","alpha-2":"SS","alpha-3":"SSD","country-code":"728","iso_3166-2":"ISO 3166-2:SS","region":"Africa","sub-region":"Sub-Saharan Africa","intermediate-region":"Eastern Africa","region-code":"002","sub-region-code":"202","intermediate-region-code":"014"},{"name":"Spain","alpha-2":"ES","alpha-3":"ESP","country-code":"724","iso_3166-2":"ISO 3166-2:ES","region":"Europe","sub-region":"Southern Europe","intermediate-region":"","region-code":"150","sub-region-code":"039","intermediate-region-code":""},{"name":"Sri Lanka","alpha-2":"LK","alpha-3":"LKA","country-code":"144","iso_3166-2":"ISO 3166-2:LK","region":"Asia","sub-region":"Southern Asia","intermediate-region":"","region-code":"142","sub-region-code":"034","intermediate-region-code":""},{"name":"Sudan","alpha-2":"SD","alpha-3":"SDN","country-code":"729","iso_3166-2":"ISO 3166-2:SD","region":"Africa","sub-region":"Northern Africa","intermediate-region":"","region-code":"002","sub-region-code":"015","intermediate-region-code":""},{"name":"Suriname","alpha-2":"SR","alpha-3":"SUR","country-code":"740","iso_3166-2":"ISO 3166-2:SR","region":"Americas","sub-region":"Latin America and the Caribbean","intermediate-region":"South America","region-code":"019","sub-region-code":"419","intermediate-region-code":"005"},{"name":"Svalbard and Jan Mayen","alpha-2":"SJ","alpha-3":"SJM","country-code":"744","iso_3166-2":"ISO 3166-2:SJ","region":"Europe","sub-region":"Northern Europe","intermediate-region":"","region-code":"150","sub-region-code":"154","intermediate-region-code":""},{"name":"Sweden","alpha-2":"SE","alpha-3":"SWE","country-code":"752","iso_3166-2":"ISO 3166-2:SE","region":"Europe","sub-region":"Northern Europe","intermediate-region":"","region-code":"150","sub-region-code":"154","intermediate-region-code":""},{"name":"Switzerland","alpha-2":"CH","alpha-3":"CHE","country-code":"756","iso_3166-2":"ISO 3166-2:CH","region":"Europe","sub-region":"Western Europe","intermediate-region":"","region-code":"150","sub-region-code":"155","intermediate-region-code":""},{"name":"Syrian Arab Republic","alpha-2":"SY","alpha-3":"SYR","country-code":"760","iso_3166-2":"ISO 3166-2:SY","region":"Asia","sub-region":"Western Asia","intermediate-region":"","region-code":"142","sub-region-code":"145","intermediate-region-code":""},{"name":"Taiwan, Province of China","alpha-2":"TW","alpha-3":"TWN","country-code":"158","iso_3166-2":"ISO 3166-2:TW","region":"Asia","sub-region":"Eastern Asia","intermediate-region":"","region-code":"142","sub-region-code":"030","intermediate-region-code":""},{"name":"Tajikistan","alpha-2":"TJ","alpha-3":"TJK","country-code":"762","iso_3166-2":"ISO 3166-2:TJ","region":"Asia","sub-region":"Central Asia","intermediate-region":"","region-code":"142","sub-region-code":"143","intermediate-region-code":""},{"name":"Tanzania, United Republic of","alpha-2":"TZ","alpha-3":"TZA","country-code":"834","iso_3166-2":"ISO 3166-2:TZ","region":"Africa","sub-region":"Sub-Saharan Africa","intermediate-region":"Eastern Africa","region-code":"002","sub-region-code":"202","intermediate-region-code":"014"},{"name":"Thailand","alpha-2":"TH","alpha-3":"THA","country-code":"764","iso_3166-2":"ISO 3166-2:TH","region":"Asia","sub-region":"South-eastern Asia","intermediate-region":"","region-code":"142","sub-region-code":"035","intermediate-region-code":""},{"name":"Timor-Leste","alpha-2":"TL","alpha-3":"TLS","country-code":"626","iso_3166-2":"ISO 3166-2:TL","region":"Asia","sub-region":"South-eastern Asia","intermediate-region":"","region-code":"142","sub-region-code":"035","intermediate-region-code":""},{"name":"Togo","alpha-2":"TG","alpha-3":"TGO","country-code":"768","iso_3166-2":"ISO 3166-2:TG","region":"Africa","sub-region":"Sub-Saharan Africa","intermediate-region":"Western Africa","region-code":"002","sub-region-code":"202","intermediate-region-code":"011"},{"name":"Tokelau","alpha-2":"TK","alpha-3":"TKL","country-code":"772","iso_3166-2":"ISO 3166-2:TK","region":"Oceania","sub-region":"Polynesia","intermediate-region":"","region-code":"009","sub-region-code":"061","intermediate-region-code":""},{"name":"Tonga","alpha-2":"TO","alpha-3":"TON","country-code":"776","iso_3166-2":"ISO 3166-2:TO","region":"Oceania","sub-region":"Polynesia","intermediate-region":"","region-code":"009","sub-region-code":"061","intermediate-region-code":""},{"name":"Trinidad and Tobago","alpha-2":"TT","alpha-3":"TTO","country-code":"780","iso_3166-2":"ISO 3166-2:TT","region":"Americas","sub-region":"Latin America and the Caribbean","intermediate-region":"Caribbean","region-code":"019","sub-region-code":"419","intermediate-region-code":"029"},{"name":"Tunisia","alpha-2":"TN","alpha-3":"TUN","country-code":"788","iso_3166-2":"ISO 3166-2:TN","region":"Africa","sub-region":"Northern Africa","intermediate-region":"","region-code":"002","sub-region-code":"015","intermediate-region-code":""},{"name":"Turkey","alpha-2":"TR","alpha-3":"TUR","country-code":"792","iso_3166-2":"ISO 3166-2:TR","region":"Asia","sub-region":"Western Asia","intermediate-region":"","region-code":"142","sub-region-code":"145","intermediate-region-code":""},{"name":"Turkmenistan","alpha-2":"TM","alpha-3":"TKM","country-code":"795","iso_3166-2":"ISO 3166-2:TM","region":"Asia","sub-region":"Central Asia","intermediate-region":"","region-code":"142","sub-region-code":"143","intermediate-region-code":""},{"name":"Turks and Caicos Islands","alpha-2":"TC","alpha-3":"TCA","country-code":"796","iso_3166-2":"ISO 3166-2:TC","region":"Americas","sub-region":"Latin America and the Caribbean","intermediate-region":"Caribbean","region-code":"019","sub-region-code":"419","intermediate-region-code":"029"},{"name":"Tuvalu","alpha-2":"TV","alpha-3":"TUV","country-code":"798","iso_3166-2":"ISO 3166-2:TV","region":"Oceania","sub-region":"Polynesia","intermediate-region":"","region-code":"009","sub-region-code":"061","intermediate-region-code":""},{"name":"Uganda","alpha-2":"UG","alpha-3":"UGA","country-code":"800","iso_3166-2":"ISO 3166-2:UG","region":"Africa","sub-region":"Sub-Saharan Africa","intermediate-region":"Eastern Africa","region-code":"002","sub-region-code":"202","intermediate-region-code":"014"},{"name":"Ukraine","alpha-2":"UA","alpha-3":"UKR","country-code":"804","iso_3166-2":"ISO 3166-2:UA","region":"Europe","sub-region":"Eastern Europe","intermediate-region":"","region-code":"150","sub-region-code":"151","intermediate-region-code":""},{"name":"United Arab Emirates","alpha-2":"AE","alpha-3":"ARE","country-code":"784","iso_3166-2":"ISO 3166-2:AE","region":"Asia","sub-region":"Western Asia","intermediate-region":"","region-code":"142","sub-region-code":"145","intermediate-region-code":""},{"name":"United Kingdom of Great Britain and Northern Ireland","alpha-2":"GB","alpha-3":"GBR","country-code":"826","iso_3166-2":"ISO 3166-2:GB","region":"Europe","sub-region":"Northern Europe","intermediate-region":"","region-code":"150","sub-region-code":"154","intermediate-region-code":""},{"name":"United States of America","alpha-2":"US","alpha-3":"USA","country-code":"840","iso_3166-2":"ISO 3166-2:US","region":"Americas","sub-region":"Northern America","intermediate-region":"","region-code":"019","sub-region-code":"021","intermediate-region-code":""},{"name":"United States Minor Outlying Islands","alpha-2":"UM","alpha-3":"UMI","country-code":"581","iso_3166-2":"ISO 3166-2:UM","region":"Oceania","sub-region":"Micronesia","intermediate-region":"","region-code":"009","sub-region-code":"057","intermediate-region-code":""},{"name":"Uruguay","alpha-2":"UY","alpha-3":"URY","country-code":"858","iso_3166-2":"ISO 3166-2:UY","region":"Americas","sub-region":"Latin America and the Caribbean","intermediate-region":"South America","region-code":"019","sub-region-code":"419","intermediate-region-code":"005"},{"name":"Uzbekistan","alpha-2":"UZ","alpha-3":"UZB","country-code":"860","iso_3166-2":"ISO 3166-2:UZ","region":"Asia","sub-region":"Central Asia","intermediate-region":"","region-code":"142","sub-region-code":"143","intermediate-region-code":""},{"name":"Vanuatu","alpha-2":"VU","alpha-3":"VUT","country-code":"548","iso_3166-2":"ISO 3166-2:VU","region":"Oceania","sub-region":"Melanesia","intermediate-region":"","region-code":"009","sub-region-code":"054","intermediate-region-code":""},{"name":"Venezuela (Bolivarian Republic of)","alpha-2":"VE","alpha-3":"VEN","country-code":"862","iso_3166-2":"ISO 3166-2:VE","region":"Americas","sub-region":"Latin America and the Caribbean","intermediate-region":"South America","region-code":"019","sub-region-code":"419","intermediate-region-code":"005"},{"name":"Viet Nam","alpha-2":"VN","alpha-3":"VNM","country-code":"704","iso_3166-2":"ISO 3166-2:VN","region":"Asia","sub-region":"South-eastern Asia","intermediate-region":"","region-code":"142","sub-region-code":"035","intermediate-region-code":""},{"name":"Virgin Islands (British)","alpha-2":"VG","alpha-3":"VGB","country-code":"092","iso_3166-2":"ISO 3166-2:VG","region":"Americas","sub-region":"Latin America and the Caribbean","intermediate-region":"Caribbean","region-code":"019","sub-region-code":"419","intermediate-region-code":"029"},{"name":"Virgin Islands (U.S.)","alpha-2":"VI","alpha-3":"VIR","country-code":"850","iso_3166-2":"ISO 3166-2:VI","region":"Americas","sub-region":"Latin America and the Caribbean","intermediate-region":"Caribbean","region-code":"019","sub-region-code":"419","intermediate-region-code":"029"},{"name":"Wallis and Futuna","alpha-2":"WF","alpha-3":"WLF","country-code":"876","iso_3166-2":"ISO 3166-2:WF","region":"Oceania","sub-region":"Polynesia","intermediate-region":"","region-code":"009","sub-region-code":"061","intermediate-region-code":""},{"name":"Western Sahara","alpha-2":"EH","alpha-3":"ESH","country-code":"732","iso_3166-2":"ISO 3166-2:EH","region":"Africa","sub-region":"Northern Africa","intermediate-region":"","region-code":"002","sub-region-code":"015","intermediate-region-code":""},{"name":"Yemen","alpha-2":"YE","alpha-3":"YEM","country-code":"887","iso_3166-2":"ISO 3166-2:YE","region":"Asia","sub-region":"Western Asia","intermediate-region":"","region-code":"142","sub-region-code":"145","intermediate-region-code":""},{"name":"Zambia","alpha-2":"ZM","alpha-3":"ZMB","country-code":"894","iso_3166-2":"ISO 3166-2:ZM","region":"Africa","sub-region":"Sub-Saharan Africa","intermediate-region":"Eastern Africa","region-code":"002","sub-region-code":"202","intermediate-region-code":"014"},{"name":"Zimbabwe","alpha-2":"ZW","alpha-3":"ZWE","country-code":"716","iso_3166-2":"ISO 3166-2:ZW","region":"Africa","sub-region":"Sub-Saharan Africa","intermediate-region":"Eastern Africa","region-code":"002","sub-region-code":"202","intermediate-region-code":"014"}]
let countryCodeToName = {}
for (let country of countries) {
    countryCodeToName[country["alpha-3"]] = country.name;
}

for (let page = 1; page <= 341; page++) {
    promises.push(axios.get(`https://api.worldbank.org/v2/countries/all/indicators/NY.GDP.MKTP.CD?page=${page}`).then(response => {
    const xmlStr = '<q id="a"><span id="b">hey!</span></q>';
    const parser = new DOMParser();
    const doc = parser.parseFromString(response.data, "application/xml");
    // 打印根元素的名称或错误信息
    const errorNode = doc.querySelector("parsererror");
    if (errorNode) {
        console.log("error while parsing");
    } else {
        console.log(doc.documentElement.nodeName);
    }
    for (let child of doc.documentElement.children) {
        let country = child.getElementsByTagName("wb:country")[0].innerHTML;
        let code = child.getElementsByTagName("wb:countryiso3code")[0].innerHTML;
        let date = child.getElementsByTagName("wb:date")[0].innerHTML;
        let value = child.getElementsByTagName("wb:value")[0].innerHTML;
        let decimal = child.getElementsByTagName("wb:decimal")[0].innerHTML;
        // console.log(country);
        if (!(date in info)) {
            info[date] = {}
        }
        // if (!(country in info[date])) {
        //     info[date][country] = {}
        // }
        info[date][code] = value;
        assert(decimal == "0");
    }
    // console.log(info["2010"]["China"]);
}));
}

Promise.all(promises).then(_ => {
    let sortable = [];
    let codes = [];
    let data = reactive({});
    for (let code in info["2010"]) {
        console.log(code);
        // console.log(code);
        if (code in countryCodeToName) {
            codes.push(code);
            sortable.push([code, info["2010"][code]]);
            data[code] = 0
            // delete countryCodeToName[code];
        }
        // if (countryCodes.has(code)) {
        //     sortable.push([code, info["2010"][code]]);
        //     countryCodes.delete(code);
        // }
    }

    sortable.sort(function(a, b) {
        return b[1] - a[1];
    });

    console.log(sortable);
    // console.log(countryCodeToName);
    console.log(info["2010"]["USA"]);
    console.log(info["2011"]["USA"]);

    const date = ref(new Date("1960-01-01"));

    watchEffect(_ => {
        for (let code of codes) {
            const infoCode = getInfo(info, date.value, code);
            data[code] = infoCode;
        }
        data["WLD"] = getInfo(info, date.value, "WLD");
    });

    const displayDate = ref(new Date(date.value));

    let going = {"going": true};

    watchEffect(() => {
        displayDate.value = new Date(date.value);
    });

    let now = ref("Bar");

    const barRace = createBarRace(codes, data, countryCodeToName, displayDate);
    const pieRace = createPieRace(codes, data, countryCodeToName, displayDate);
    const mapRace = createMapRace(codes, data, countryCodeToName, displayDate);
    const button = createButton(date, displayDate, going);
    // barRace.node().style.position = "absolute";
    // pieRace.node().style.position = "absolute";
    // button.node().style.position = "absolute";
    // button.node().style.top = "640";

    let nowRace = barRace;

    // barRace
    //     .style("opacity", 0)
    //     .transition()
    //     .duration(1500)
    //     .style("opacity", 1);
    // document.getElementById("container").append(barRace.node());

    // barRace.node().remove();
    
    // function change() {
    //     if (now == "Bar") {
    //         barRace.node().remove();
    //     }
    // }
    
    // document.getElementById("container").append(pieRace.node());
    document.getElementById("container").append(button.node());    

    const star = document.createElement("span");
    star.setAttribute("class", "fa fa-play");
    star.style.position = "absolute";
    star.style.top = "697px";
    star.style.right = "1339px";
    star.style.color = "white";
    star.style.pointerEvents = "none";
    document.getElementById("container").append(star);

    setInterval(() => {
        if (date.value > new Date("2021-01-01")) {
            going.going = false;
        }
        if (going.going) {
            displayDate.value = new Date(displayDate.value.setDate(displayDate.value.getDate() + 1))
            if (displayDate.value.getMonth() % 1 == 0 && (displayDate.value.getDate() % 10 == 0)) {
                date.value = new Date(displayDate.value);
            }
            star.setAttribute("class", "fa fa-pause");
        } else {
            star.setAttribute("class", "fa fa-play");
        }
    }, 100 / 365);

    // svg.append("span")
    //     .attr("class", "fa fa-star");

    const changeBtnSpan = document.createElement('div');
    changeBtnSpan.style.display = "inline-block";
    changeBtnSpan.style.borderRadius = "10px";
    changeBtnSpan.style.border = "1.6px solid Steelblue";
    changeBtnSpan.style.overflow = "hidden";
    changeBtnSpan.style.position = "relative";
    changeBtnSpan.style.top = "20px";

    function createChangeBtn(name, race) {
        const changeBtn = document.createElement('button');
        changeBtnSpan.appendChild(changeBtn);
        changeBtn.class = "btn";
        changeBtn.textContent = name;
        changeBtn.style.border = "Steelblue";
        changeBtn.style.padding = "10px 20px";
        changeBtn.style.transition = "background-color 0.3s, color 0.3s";
        
        changeBtn.onclick = () => {
            now.value = name;
        };
        watchEffect(() => {
            if (now.value == name) {
                nowRace.node().remove();
                const container = document.getElementById("container");
                race
                    .style("opacity", 0.33)
                    .transition()
                    .duration(500)
                    .ease(d3.easeCubicOut)
                    .style("opacity", 1);
                container.insertBefore(race.node(), container.firstChild);
                nowRace = race;

                changeBtn.style.backgroundColor = "Steelblue";
                changeBtn.style.color = "White";    
            } else {
                changeBtn.style.backgroundColor = "Transparent";
                changeBtn.style.color = "Steelblue";
            }
        });
        return changeBtn;
    }

    const changeBtn1 = createChangeBtn('Bar', barRace);
    const changeBtn2 = createChangeBtn('Pie', pieRace);
    const changeBtn3 = createChangeBtn('Map', mapRace);
    document.getElementById("container").append(changeBtnSpan);

    // for (let i = 0; i < count; i++) {
    //     watchEffect(() => {
    //         console.log(y(length[i].value));
    //         bars[i].transition().duration(1000).attr("y", y(i + 1 - 0.3)).attr("height", y(i + 1 + 0.3) - y(i + 1 - 0.3));
    //     })
    // }    
});

function getInfo(info, date, code) {
    // console.log("date");
    // console.log(date);
    let thisYear = new Date(date.getFullYear(), 0, 1, date.getHours(), date.getUTCMinutes(), date.getSeconds(), date.getMilliseconds());
    // console.log(thisYear);
    let nextYear = new Date(date.getFullYear() + 1, 0, 1, date.getHours(), date.getMinutes(), date.getSeconds(), date.getMilliseconds());
    let thisYearPassed = date - thisYear;
    let nextYearToPass = nextYear - date;
    
    // console.log(`${date.getFullYear()}`);
    // console.log(info[`${date.getFullYear()}`][code]);
    let thisYearInfo = parseFloat(info[`${date.getFullYear()}`][code]);
    let nextYearInfo = parseFloat(info[`${date.getFullYear() + 1}`][code]);
    // console.log(thisYearInfo);
    // console.log(nextYearInfo);
    // console.log(code)
    // console.log(thisYearInfo);
    // console.log((thisYearInfo * nextYearToPass + nextYearInfo * thisYearPassed) / (thisYearPassed + nextYearToPass));
    return (thisYearInfo * nextYearToPass + nextYearInfo * thisYearPassed) / (thisYearPassed + nextYearToPass);
}

function display(x) {
    return (Math.round(x * 100) / 100).toFixed(2).toString().replace(/\B(?=(\d{3})+(?!\d))/g, ",");
}

console.log(new Date("2024-01-01"));
let date = new Date("2021-04-18T00:00:00.000+00:00")
// let utcDate = new Date(Date.UTC(date.getUTCFullYear(), date.getUTCMonth(), date.getUTCDate(), date.getUTCHours(), date.getUTCMinutes(), date.getUTCSeconds(), date.getUTCMilliseconds()));

function createButton(date, displayDate, going) {
    const width = 1000;
    const height = 50;
    const svg = d3.create("svg")
            .attr("width", width)
            .attr("height", height);
    
    function b() {
        return () => {
            date.value.setDate(date.value.getDate() + 100);
            date.value = new Date(date.value);
        }
    }

    function s() {
        console.log(xBegin);
        console.log(new Date("1960-01-01").getDate());
        console.log('date');
        console.log(new Date("1960-01-01"));
        console.log();
        if (xBegin != undefined) {
            if (xBegin < 0) {
                date.value = new Date(new Date("1960-01-01").setMilliseconds((new Date("2021-01-01") - new Date("1960-01-01")) / 400 * 0));
            } else if (xBegin > 400) {
                date.value = new Date(new Date("1960-01-01").setMilliseconds((new Date("2021-01-01") - new Date("1960-01-01")) / 400 * 400));
            } else {
                date.value = new Date(new Date("1960-01-01").setMilliseconds((new Date("2021-01-01") - new Date("1960-01-01")) / 400 * xBegin));
            }
            displayDate.value = new Date(date.value);
        }
    }

    const x = d3.scaleUtc()
        .domain([new Date("1960-01-01"), new Date("2021-01-01")])
        .range([300, 700]);

    // Add the x-axis.
    const gx = svg.append("g")
        .attr("transform", `translate(0, 30)`)
        .call(d3.axisBottom(x))
        .style("z-index", "10");

    
    let nextTranslateX = 0;
    let xBegin;
    let yBegin;
    let time = new Date();
    const transform = [0, 0];
    const dragButton = svg.append("circle")
        .attr("cx", 300)
        .attr("cy", 30)
        .attr("r", 12)
        .on("click", b)
        .on("mouseover", function(d) {
                d3.select(this).transition().duration(300).style("fill", "skyblue").style("cursor", "pointer").attr("r", 20);
            })
        .on("mouseout", function(d) {
                d3.select(this).transition().duration(300).style("fill", "steelblue").style("cursor", "default").attr("r", 12);
            }
        )
        .style("fill", d3.color("steelblue"))
        .call(d3.drag()
            .on("start", function(event) {
            })
            .on("drag", function(event) {
                const [x_, y_] = d3.pointer(event);
                const gxRect = gx.node().getBoundingClientRect();
                const x = x_ - gxRect.left;
                const y = y_ - gxRect.top;
                if (xBegin != undefined && yBegin != undefined) {
                    transform[0] += x - xBegin;
                    transform[1] += y - yBegin;
                    const newTime = new Date();
                    if (newTime - time > 40) {
                        s();
                        time = newTime;
                    }
                }
                console.log(gxRect.left);
                console.log(x);
                [xBegin, yBegin] = [x, y];
                let now = transform[0];
                if (now < 0) {
                    now = 0;
                }
                if (now > 400) {
                    now = 400;
                }
                d3.select(this)
                    // .attr("transform", `translate(${now}, 0)`)
                    .transition().duration(100)
                    .style("fill", "skyblue")
                    .style("cursor", "pointer")
                    .attr("r", 20);
            })
            .on("end", function() {
                xBegin = undefined;
                yBegin = undefined;
                const newTime = new Date();
                s();
                time = newTime;
            })
        )

    watchEffect(_ => {
        const translateX = 400. * (date.value - new Date("1960-01-01")) / (new Date("2021-01-01") - new Date("1960-01-01"));
        dragButton.attr("transform", `translate(${translateX}, 0)`);
        
        // if (xBegin == undefined) {
        //     // dragButton.attr("cx", 300. + translateX);
        //     dragButton.attr("transform", `translate(${translateX}, 0)`);
        // }
    });

    const goingButton = svg.append("circle")
        .attr("cx",  30)
        .attr("cy", 30)
        .attr("r", 12)
        .on("click", () => going.going = !going.going)
        .on("mouseover", function(d) {
                d3.select(this).transition().duration(300).style("fill", "skyblue").style("cursor", "pointer").attr("r", 20);
            })
        .on("mouseout", function(d) {
                d3.select(this).transition().duration(300).style("fill", "steelblue").style("cursor", "default").attr("r", 12);
            }
        )
        .style("fill", d3.color("steelblue"));

    const i = svg.append("span")
        .attr("class", "fa fa-star");

    const triangle = svg.append("path")
        .attr("d", d3.symbol()
            .type(d3.symbolTriangle)
            .size(6))
            .attr("x", 30)
            .attr("y", 30)
            .attr("fill", d3.color("deepblue"));

    return svg;
}

class ClickButton {
  constructor(name, url) {
    this.name = name;
    this.url = url;
  }
}

function sort(names, data) {
    const sortable = [];
    const rank = {}

    for (let name of names) {
        if (!isNaN(data[name])) {
            sortable.push([name, data[name]]);
        }
    }

    // console.log(sortable);

    sortable.sort(function(a, b) {
        return b[1] - a[1];
    });

    // console.log(sortable);

    for (let i = 0; i < sortable.length; i++) {
        const name = sortable[i][0];
        if (i < 10) {
            rank[name] = i + 1;
        } else {
            rank[name] = 15;
        }
    }
    return [sortable, rank];
}

function createPieRace(names, data, dict, displayDate) {
    const width = 1240;
    const height = 640;
    const svg = d3.create("svg")
            .attr("width", width)
            .attr("height", height);
    
    const paths = {};
    const texts = {};
    const values = {};
    const lines = {};

    const centerX = height/2-70;
    const centerY = width/2-170;
    const outY = -width/2-170;

    nowInfo(svg, data, displayDate, 800, height/2 - 60);

    watchEffect(_ =>{
        const [sortable, rank] = sort(names, data);
        const sortableRanking10 = sortable.slice(0, 10);
        const dataDict = {};
        let sum = 0;
        for (let i = 0; i < 10; i++) {
            sum += sortableRanking10[i][1];
            dataDict[sortableRanking10[i][0]] = sortableRanking10[i][1];
        }
        sortableRanking10.push(["Other", data["WLD"] - sum]);
        dataDict["Other"] = data["WLD"] - sum;
        const pie = d3.pie()
            .sort(null)
            .padAngle(0.015)
            .value(d => d[1]);
        const arcs = pie(sortableRanking10);
        const namesInArcs = {};
        // console.log(arcs);
        for (let arc of arcs) {
            if (!(arc.data[0] in paths)) {
                paths[arc.data[0]] = svg.append("path")
                    .attr("transform", `translate(${centerY},${centerX})`)
                    .style("fill", d3.color("steelblue"));
                texts[arc.data[0]] = svg.append("text")
                    .attr("transform", "translate(" + (outY) + "," + (centerX) + ")")
                    .style("fill", d3.color("steelblue"))
                    .style("font-size", "18px");
                values[arc.data[0]] = svg.append("text")
                    .attr("dy", "1.2em")
                    .attr("transform", "translate(" + (outY) + "," + (centerX) + ")")
                    .style("fill", d3.color("steelblue"))
                    .style("font-size", "12px");
                lines[arc.data[0]] = svg.append("line")
                    .attr("x1", (width/2-170))
                    .attr("y1", (height/2))
                    .attr("x2", (width/2-170))
                    .attr("y2", (height/2))
                    .style("stroke", d3.color("steelblue"));
            }
            namesInArcs[arc.data[0]] = arc;
        }
        for (let name in paths) {
            const path = paths[name];
            const text = texts[name];
            const value = values[name];
            const line = lines[name];
            const country = name in dict ? dict[name] : "Other";
            if (name in namesInArcs) {
                const arc = namesInArcs[name];
                // if (arc["endAngle"] - arc["startAngle"] > 0.05) { }
                path
                    .transition()
                    .duration(500)
                    .ease(d3.easeLinear)
                    .attr("d", d3.arc()({
                        innerRadius: 100,
                        outerRadius: 150,
                        startAngle: arc["startAngle"],
                        endAngle: arc["endAngle"],
                        // padAngle: (arc["endAngle"] - arc["startAngle"]) * 0.02,
                        padAngle: arc["padAngle"],
                    }))
                    .style("opacity", 1);
                const meanAngle = (arc["startAngle"] + arc["endAngle"]) / 2;
                text
                    .transition()
                    .duration(500)
                    .ease(d3.easeLinear)
                    .attr("transform", "translate(" + (centerY + 300 * Math.sin(meanAngle)) + "," + (centerX - 300 * Math.cos(meanAngle)) + ")")
                    .style("opacity", 1)
                    .text(country + "(" + display(dataDict[name] / data["WLD"] * 100) + "%)");
                value
                    .transition()
                    .duration(500)
                    .ease(d3.easeLinear)
                    .attr("transform", "translate(" + (centerY + 300 * Math.sin(meanAngle)) + "," + (centerX - 300 * Math.cos(meanAngle)) + ")")
                    .style("opacity", 0.75)
                    .text(display(dataDict[name]));
                line
                    .transition()
                    .duration(500)
                    .ease(d3.easeLinear)
                    .attr("x1", (centerY + 150 * Math.sin(meanAngle)))
                    .attr("y1", (centerX - 150 * Math.cos(meanAngle)))
                    .attr("x2", (centerY + 270 * Math.sin(meanAngle)))
                    .attr("y2", (centerX - 270 * Math.cos(meanAngle)))
                    .style("opacity", 0.5);
            } else {
                path
                    .transition()
                    .duration(500)
                    .ease(d3.easeLinear)
                    .attr("d", d3.arc()({
                        innerRadius: 0,
                        outerRadius: 0,
                        startAngle: 6.28,
                        endAngle: 6.28,
                        padAngle: 0,
                    }))
                    .style("opacity", 0);
                text
                    .transition()
                    .duration(500)
                    .ease(d3.easeLinear)
                    .attr("transform", "translate(" + (outY) + "," + (centerX) + ")")
                    .style("opacity", 0)
                    .text("");
                value
                    .transition()
                    .duration(500)
                    .ease(d3.easeLinear)
                    .attr("transform", "translate(" + (outY) + "," + (centerX) + ")")
                    .style("opacity", 0)
                    .text("");
                line
                    .transition()
                    .duration(500)
                    .ease(d3.easeLinear)
                    .attr("x1", (centerY))
                    .attr("y1", (centerX))
                    .attr("x2", (centerY))
                    .attr("y2", (centerX))
                    .style("opacity", 0);
            }
        }
    });
    
    // const arc = d3.svg.arc()
    //     .innerRadius(0)
    //     .outerRadius(1);
    // const arcs = svg.selectAll("g")
    //     .data(pie(sortable))
    //     .enter()
    //     .append("g")
    //     .attr("transform", `translate(${width/2},${height/2})`);
    // arcs.append("path")
    //     .attr("fill", function(d, i) {
    //         return color(i);
    //     })
    //     .attr("d", function(d) {
    //         return arc(d);
    //     });
    
    return svg;
}

function createMapRace(names, data, dict, displayDate) {
    const width = 1240;
    const height = 640;
    const svg = d3.create("svg")
            .attr("width", width)
            .attr("height", height);

    const projection = d3.geoNaturalEarth1()
        .scale(width / 2. / Math.PI)
        .translate([width / 2, height / 2]);

    // svg.append('path')
        // .attr('class', 'sphere')
        // .attr('d', pathGenerator({type: 'Sphere'}));

    const countryNameText = svg.append("text")
        .attr("x", 0)
        .attr("y", 360)
        .attr("dy", ".435em")
        .style("fill", d3.color("steelblue"))
        .style("font-size", "24px");

    const gdpText = svg.append("text")
        .attr("x", 0)
        .attr("y", 390)
        .attr("dy", ".435em")
        .style("fill", d3.color("steelblue"))
        .style("font-size", "12px");

    d3.json('https://raw.githubusercontent.com/holtzy/D3-graph-gallery/master/DATA/world.geojson').then(data_ => {
        const path = svg.selectAll('path')
            .data(data_.features)
            .enter()
            .append('path')
            .style("cursor", "pointer")
            .style("stroke", d3.color("steelblue"))
            .attr("id", d => "map-" + d.id)
            .attr("class", "map")
            .attr("fill", d3.color("transparent"))
            .attr('d', d3.geoPath().projection(projection))
            .on("mouseover", (e, d) => {
                const countryName = dict[d.id];
                if (!isNaN(data[d.id])) {
                    gdpText.text(display(data[d.id]));
                    countryNameText.text(dict[d.id] + " (" + display(data[d.id] / data["WLD"] * 100) + "%)");
                } else {
                    gdpText.text("No data");
                    countryNameText.text(dict[d.id]);
                }
                watchEffect(_ => {
                    
                });
            });
        console.log(path);
    });

    function mapColor(x) {
        return Math.sqrt(x) * 1.3 + 0.1;
    }

    const interpolate = d3.interpolateLab("transparent", d3.interpolateLab("steelblue", "skyblue")(0.5));

    const y = d3.scaleLinear()
        .domain([0, 0.4])
        .range([300, 100]);

    // Add the y-axis.
    const gy = svg.append("g")
        .attr("transform", `translate(40, 0)`)
        .call(d3.axisLeft(y).tickFormat(d3.format('~%')));

    // const colorScale = d3.scaleLinear()
    //     .domain([0, 0.4])
    //     .range([d3.interpolate(mapColor(0)), d3.interpolate(mapColor(0.4))]);

    // console.log(colorScale(0.2));

    const split = 200;
    for (let i = 0; i < split; i++) {
        svg.append("rect")
            .attr("x", 40)
            .attr("y", y((i + 1) * 0.4 / split))
            .attr("width", 20)
            .attr("height", y(i * 0.4 / split) - y((i + 1) * 0.4 / split))
            .style("stroke", d3.color("transparent"))
            .style("fill", interpolate(mapColor(i * 0.4 / split)));
    }

    // svg.append("g")
    //     .attr("transform", "translate(500,10)")
    //     .call(legend);

    // const maps = d3.selectAll(".map");
    // console.log(maps);
    // for (let map of maps) {
    //     console.log(map);
    //     map.onmouseover = () => {
    //         countryNameText.text(map.attr("id"));
    //     };
    // }

    watchEffect(_ =>{
        const [sortable, rank] = sort(names, data);
        for (let name of names) {
            const map = document.getElementById("map-" + name);
            if (map != undefined) {
                // console.log(name, interpolate(data[name] / data["WLD"]));
                // console.log(map.style.stroke, typeof(interpolate(data[name] / data["WLD"])));
                // console.log(name, data[name]);
                if (!isNaN(data[name])) {
                    map.setAttribute('fill', interpolate(mapColor(data[name] / data["WLD"])));
                } else {
                    map.setAttribute('fill', interpolate(0));
                }
            }
        }
    });
    
    nowInfo(svg, data, displayDate, 0, height/2 + 150);

    return svg;
}

function nowInfo(svg, data, displayDate, x, y) {
    const yearText = svg.append("text")
            .attr("x", x)
            .attr("y", y)
            .attr("dy", ".435em")
            .style("fill", d3.color("steelblue"))
            .style("font-size", "72px");

    watchEffect(() => {
        yearText
            .text(displayDate.value.getFullYear());
            // .text(date.value.toDateString());
    });

    const dateText = svg.append("text")
            .attr("x", x + 175)
            .attr("y", y + 13)
            .attr("dy", ".435em")
            .style("fill", d3.color("steelblue"))
            .style("font-size", "40px");
    
    watchEffect(() => {
        dateText
            .text(displayDate.value.getMonth() + 1 + "/" + displayDate.value.getDate());
            // .text(date.value.toDateString());
    });

    const world = svg.append("text")
            .attr("x", x)
            .attr("y", y + 70)
            .attr("dy", ".435em")
            .style("fill", d3.color("steelblue"))
            .style("font-size", "24px");

    watchEffect(() => {
        world.text("World total: " + display(data["WLD"]));
    });
}

function createBarRace(names, data, dict, displayDate) {
    const length = Array(names.length).fill().map(() => ref(0));

    const width = 1240;
    const height = 640;
    const marginTop = 20;
    const marginRight = 20;
    const marginBottom = 30;
    const marginLeft = 40;

    const svg = d3.create("svg")
            .attr("width", width)
            .attr("height", height);
            

    const x = d3.scaleLinear()
        .domain([0, 1])
        .range([marginLeft, width - marginRight]);

    // Declare the y (vertical position) scale.
    const y = d3.scaleLinear()
        .domain([11, 0])
        .range([height - marginBottom, marginTop]);

    // Add the x-axis.
    let gx = svg.append("g")
        .attr("transform", `translate(0,${height - marginBottom})`)
        .call(d3.axisBottom(x));

    // Add the y-axis.
    let gy = svg.append("g")
        .attr("transform", `translate(${marginLeft},0)`)
        .call(d3.axisLeft(y));

    nowInfo(svg, data, displayDate, 800, 480);

    // Create the SVG container.

    const bars = {};
    const texts = {};
    const values = {};

    for (let name of names) {
        bars[name] = svg.append("rect")
            .attr("x", x(0))
            .attr("y", y(15 - 0.3))
            .attr("width", 0)
            .attr("height", y(15 + 0.3) - y(15 - 0.3))
            .style("fill", d3.color("steelblue"));
        texts[name] = svg.append("text")
            .attr("x", x(0))
            .attr("y", y(15))
            .attr("dy", "-2px")
            .text(dict[name])
            .style("fill", d3.color("steelblue"))
            .style("font-size", "18px");
        values[name] = svg.append("text")
            .attr("x", x(0))
            .attr("y", y(15))
            .attr("dy", "16px")
            .style("fill", d3.color("steelblue"))
            .style("font-size", "12px");
    }

    let timeTrans = new Date();

    watchEffect(_ => {
        const [sortable, rank] = sort(names, data);

        const max = sortable[0][1];
        const duration = 500;

        x
            .domain([0, max * 1.2])
            .range([marginLeft, width - marginRight]);

        gx.transition()
            .duration(duration)
            .ease(d3.easeLinear)
            .call(d3.axisBottom(x));
        
        for (let name of names) {
            let d, r;
            if (!isNaN(data[name])) {
                d = data[name];
                r = rank[name];
            } else {
                d = 0;
                r = 15;
            }
            const newTimeTrans = new Date();
            if (newTimeTrans - timeTrans > duration) {
                timeTrans = newTimeTrans;
            }
            bars[name]
                .transition()
                .duration(duration)
                .ease(d3.easeLinear)
                .attr("x", x(0))
                .attr("width", x(d) - x(0))
                .attr("y", y(r - 0.3))
                .attr("height", y(r + 0.3) - y(r - 0.3));
            texts[name]
                .transition()
                .duration(duration)
                .ease(d3.easeLinear)
                .attr("x", x(d) + 5)
                .attr("y", y(r));
            values[name]
                .transition()
                .duration(duration)
                .ease(d3.easeLinear)
                .attr("x", x(d) + 5)
                .attr("y", y(r))
                .text(display(data[name]));
        }
    });

    return svg;
}

onMounted(() => {
    // for (let src of [
    //     "https://d3js.org/d3.v6.js", 
    //     "https://d3js.org/d3-scale-chromatic.v3.min.js", 
    //     "https://d3js.org/d3-geo-projection.v4.min.js"]) {
    //     const script = document.createElement('script');
    //     script.setAttribute('src', src);
    //     document.head.appendChild(script);
    // }

    const stylesheet = document.createElement('link');
    stylesheet.setAttribute('rel', 'stylesheet');
    stylesheet.setAttribute('href', 'https://cdnjs.cloudflare.com/ajax/libs/font-awesome/4.7.0/css/font-awesome.min.css');
    document.head.appendChild(stylesheet);
})

function a() {
    console.log(gy);

    const width = 640;
    const height = 400;
    const marginTop = 20;
    const marginRight = 20;
    const marginBottom = 30;
    const marginLeft = 40;

    // Declare the x (horizontal position) scale.
    const x = d3.scaleUtc()
        .domain([new Date("2023-01-01"), new Date("2024-01-01")])
        .range([marginLeft, width - marginRight]);

    // Declare the y (vertical position) scale.
    const y = d3.scaleLinear()
        .domain([0, 100])
        .range([height - marginBottom, marginTop]);

    const y2 = d3.scaleLinear()
        .domain([0, 50])
        .range([height - marginBottom, marginTop]);

    gy.transition()
    .duration(3000)
    .call(d3.axisLeft(y2))
    .transition()
    .duration(3000)
    .call(d3.axisLeft(y));
}

</script>

<template>
    <div id="container"></div>
    <!-- <div class="container-fluid min-vh-100 d-flex flex-column">
        <div id="container" class="row flex-grow-4" style="height: 1240; width: 640;"/>
        <div id="buttons" class="row flex-grow-1"/>
    </div> -->
    <!-- <button @click="b">a</button> -->
    <!-- <span v-html="svg.node().html()"/> -->
</template>
