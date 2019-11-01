<p>Emerald Onion would not exist without the generosity of people, including the people behind Emerald Onion who are all volunteers. Grants and donations make up 100% of our income, and we would sincerely welcome your help. Being a U.S. tax-deductible nonprofit, we depend on grants and donations for deploying and maintaining privacy infrastructure.</p>

<p>If you are interested in donating time or IPv4 space, or volunteering, please contact us via Signal (+1-206-739-3390), Wire (EmeraldOnion), Twitter (EmeraldOnion) or Email (donations at emeraldonion.org) with questions.</p>

<p>Emerald Onion is a U.S. 501(c)(3) nonprofit, tax ID #82-2009438. Contributions are tax deductible as allowed by law. Thank you!</p>

# PayPal Giving Fund

<a href="https://www.paypal.com/us/fundraiser/charity/2819730" target="_blank">https://www.paypal.com/us/fundraiser/charity/2819730</a>

# Bitcoin

 <style>
  .bitpay-donate { margin:20px 0;}
  .bitpay-donate fieldset {border:0;}
  .bitpay-donate .field-input { color: #666;background: #fff;border: 1px solid #eee;height: 30px;box-sizing: border-box;flex-grow: 1;}
  .bitpay-donate .field-input-wrapper { display: inline-flex;float: none;width: 80%; }
  .bitpay-donate input {padding:4px 10px;}
  .bitpay-donate select {padding:3px 10px;}
  .bitpay-donate .bitpay-donate-button {padding:12px 0;width: 188px;box-sizing: border-box;}
  .bitpay-donate ul, .bitpay-donate li {padding:0;margin:0;list-style:none;}
  .bitpay-donate li {padding:10px 0;}
  .bitpay-donate-field {clear:both;}
  .bitpay-donate-field label {float:left;width:100px;}
  .bitpay-donate-field div {float:left;}
  .bitpay-donate-field-email {width:80%;}
  .bitpay-donate-field-price {width:40%;}
  .bitpay-donate-field-currency {width:40%;}
  .bitpay-donate-button-wrapper {clear:both;margin:auto;text-align:center;}
  input.bitpay-donate-error {border:2px solid red;}
  </style>
  <form class="bitpay-donate" action="https://bitpay.com/checkout" method="post" onsubmit="return checkRequiredFields(this);">
    <input name="action" type="hidden" value="checkout">
    <fieldset>
      <ul>
        <li class="bitpay-donate-field">
          <label>Email:</label>
          <input class="bitpay-donate-field-email field-input" name="buyerEmail" type="email" placeholder="Email address (optional)" maxlength=50 autocapitalize=off autocorrect=off><br>
        </li>
        <li class="bitpay-donate-field">
          <label>Amount:</label>
          <div class="field-input-wrapper">
            <input class="bitpay-donate-field-price field-input" name="price" type="number" value="10.00" placeholder="Amount" maxlength="10" min="0.000006" step="0.000001"/>
            <select class="bitpay-donate-field-currency field-input" name="currency" value="">
              <option selected="selected" value="USD">USD</option>
              <option value="BTC">BTC</option>
              <option value="EUR">EUR</option>
              <option value="GBP">GBP</option>
              <option value="AUD">AUD</option>
              <option value="BGN">BGN</option>
              <option value="BRL">BRL</option>
              <option value="CAD">CAD</option>
              <option value="CHF">CHF</option>
              <option value="CNY">CNY</option>
              <option value="CZK">CZK</option>
              <option value="DKK">DKK</option>
              <option value="HKD">HKD</option>
              <option value="HRK">HRK</option>
              <option value="HUF">HUF</option>
              <option value="IDR">IDR</option>
              <option value="ILS">ILS</option>
              <option value="INR">INR</option>
              <option value="JPY">JPY</option>
              <option value="KRW">KRW</option>
              <option value="LTL">LTL</option>
              <option value="LVL">LVL</option>
              <option value="MXN">MXN</option>
              <option value="MYR">MYR</option>
              <option value="NOK">NOK</option>
              <option value="NZD">NZD</option>
              <option value="PHP">PHP</option>
              <option value="PLN">PLN</option>
              <option value="RON">RON</option>
              <option value="RUB">RUB</option>
              <option value="SEK">SEK</option>
              <option value="SGD">SGD</option>
              <option value="THB">THB</option>
              <option value="TRY">TRY</option>
              <option value="ZAR">ZAR</option>
            </select>
          <div>
        </li>
      </ul>
      <input type="hidden" name="data" value="0QMnyDLKU1KGow3bLZEoJvkoyHYjYzLBratISCxfr/MEFGHDhmKd/rgjAyUbCQzd15vehaUNBGu7Kqv6RUOU5OP4UPVjDGrTEL+jbucYymzUj5cqcanP7sjFOHgekrLt/MoqD78+taiBLFFdOpVH0ix9f9vqo1BYELVQr8WLOjZAZIBbSanMrIOFzFTNgUBCYpJFvx47I+huwB5cusOMSKGtqouC25fAL1dQ6UZWJEptBTkhCL3NWlrKQqPNa+FivzXJZXA5d30XfiPaxojgZw=="> 
      <div class="bitpay-donate-button-wrapper">
        <input class="bitpay-donate-button" name="submit" src="https://bitpay.com/cdn/en_US/bp-btn-donate-currencies.svg" onerror="this.onerror=null; this.src='https://bitpay.com/cdn/en_US/bp-btn-donate-currencies.svg'" type="image" alt="BitPay, the easy way to pay with bitcoins.">
      </div>
    </fieldset>
  </form>
  <script type="text/javascript">
  function checkRequiredFields(form){
    var elements = form.elements;
    var invalid = false;
    for(var i=0; i<elements.length; i++) {
      elements[i].className = elements[i].className.replace('bitpay-donate-error', '');
      if (elements[i].className.indexOf("required") !== -1 && elements[i].value.length < 1) {
        elements[i].className = elements[i].className + ' bitpay-donate-error';
        invalid = true;
      };
    }
    if (invalid) { return false; }
    var donationElement = document.getElementById('donation-value');
    if(donationElement){
      var enteredDonation = Number(donationElement.value);
      var maximumDonation = Number(document.getElementById('reference-maximum').value);
      if(enteredDonation > maximumDonation){ 
        alert("Your donation was larger than the allowed maximum of " + Number(maximumDonation).toFixed(2))
        return false;
      };
    };
    var buyerEmailField = document.querySelector('[name="buyerEmail"]');
    var orderIdField = document.querySelector('[name="orderID"]');
    if (buyerEmailField && orderIdField) {
      orderIdField.value = buyerEmailField.value;
    }
    return true;
  };
  </script>

# Benevity

Your company might match donations (like if you work for Apple, Google, or Microsoft)!

<a href="https://causes.benevity.org/causes/840-822009438" target="_blank">https://causes.benevity.org/causes/840-822009438</a>

# AmazonSmile

<a href="https://smile.amazon.com/hz/charitylist/ls/HK9UAWTA5YLR/ref=smi_ext_lnk_lcl_cl" target="_blank">Emerald Onion's charity list for infrastructure in Seattle, WA, United States</a>

<a href="https://smile.amazon.com/hz/charitylist/ls/1PITW87JXW5AG/ref=smi_ext_lnk_lcl_cl" target="_blank">Emerald Onion's charity list for infrastructure in Montreal, Québec, Canada</a>

# Teespring

<a href="https://teespring.com/emerald-onion" target="_blank">https://teespring.com/emerald-onion</a>

# Cash, Check, or Money Order

<address>Emerald Onion
<br />815 1st Ave # 331
<br />Seattle, WA 98104-1404</address>
<br />
<br />
