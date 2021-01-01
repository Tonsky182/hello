# hello
DOCTYPE html>
<html>
<head>
  <title>Freedogeon The Moon Game - Game Verification</title>
  <meta http-equiv="content-type" content="text/html; charset=UTF-8">
  <meta name="robots" content="noindex, nofollow">
  <meta name="googlebot" content="noindex, nofollow">
  <meta name="viewport" content="width=device-width, initial-scale=1">
  <script type="text/javascript" src="//cdnjs.cloudflare.com/ajax/libs/jquery/3.2.1/jquery.js"></script>
  <link rel="stylesheet" type="text/css" href="//cdnjs.cloudflare.com/ajax/libs/bulma/0.6.2/css/bulma.min.css">
  <link rel="stylesheet" type="text/css" href="//cdnjs.cloudflare.com/ajax/libs/font-awesome/4.7.0/css/font-awesome.min.css">
  <script type="text/javascript" src="//cdnjs.cloudflare.com/ajax/libs/crypto-js/3.1.9-1/crypto-js.min.js"></script>
  
  <style>
    *, body, button, input, textarea, select {
      text-rendering: optimizeLegibility;
      -moz-osx-font-smoothing: grayscale;
    }
    body,div,dl,dt,dd,ul,ol,li,h1,h2,h3,h4,h5,h6,pre,form,fieldset,input,textarea,p,blockquote,th,td {
      margin:0;
      padding:0;
    }
    table {
      border-collapse:collapse;
      border-spacing:0;
    }
    fieldset,img {
      border:0;
    }
    address,caption,cite,code,dfn,em,strong,th,var {
      font-style:normal;
      font-weight:normal;
    }
    ol,ul {
      list-style:none;
    }
    caption,th {
      text-align:left;
    }
    h1,h2,h3,h4,h5,h6 {
      font-size:100%;
      font-weight:normal;
    }
    q:before,q:after {
      content:'';
    }
    abbr,acronym { border:0;}
  </style>
  <style type="text/css">
    table {
        table-layout: fixed;
    }
    table thead tr th:first-child {
        width: 80%;
    }
    table tbody tr td {
        white-space: nowrap;
        overflow: hidden;
        text-overflow: ellipsis;
    }
    .is-first {
        background-color: rgba(195, 111, 24, 0.2);
    }
    .is-over-median {
        color: #44B39D;
    }
    .is-at-median {
        color: #3B3C3D;
    }
    .is-under-median {
        color: #BF4A67;
    }
  </style>
</head>

<body>
  <section class="section">
    <div class="container">
      <h1 class="title">Freedogeon The Moon Game - Game Verification</h1>
      <h2 class="subtitle">Third party script used to verify games on crash game.</h2>
    </div>
    <hr>
    <div class="container">
      <p>The following sites have purchased a non-distributable copy of the previous version of freedogeon’s source code, exempting them from the requirements of the AGPL:</p>
      <p><a target="_blank" href="https://freedogeon.com">https://freedogeon.com</a></p>
    </div>
    <hr>
    <div class="container">
      <p>You can view or download historical statistics here.</p>
      <p><a target="_blank" href="https://freedogeon.github.io/HashCheck/#/simple">https://freedogeon.github.io/HashCheck/#/simple</a></p>
    </div>
    <hr>
    <div class="container">
      <p>The source code.</p>
      <p><a target="_blank" href="https://github.com/freedogeon/the-moon-game">https://github.com/freedogeon/the-moon-game</a></p>
    </div>
    <hr>
    <div class="container">
      <div class="field">
        <p class="control has-icons-left">
          <input class="input" type="text" id="game_hash_input" placeholder="Game's hash (SHA256)">
          <span class="icon is-small is-left"><i class="fa fa-key"></i></span>
        </p>
      </div>
      <div class="field">
        <p class="control has-icons-left">
          <input class="input" type="number" id="game_amount_input" min="1" max="100000" step="1" placeholder="Amount of games" value="10">
          <span class="icon is-small is-left"><i class="fa fa-hashtag"></i></span>
        </p>
      </div>
      <div class="field is-grouped">
        <p class="control">
          <a class="button is-primary" id="game_verify_submit">Verify</a>
        </p>
      </div>
    </div>
    <hr>
    <div class="container">
      <table class="table is-striped is-fullwidth is-hoverable is-narrow" style="display:table">
        <thead>
          <tr>
            <th><b>Game's hash</b></th>
            <th><b>Bust</b></th>
          </tr>
        </thead>
        <tbody id="game_verify_table"></tbody>
      </table>
    </div>
  </section>
    <script type="text/javascript">
      var isVerifying = false;
      $('#game_verify_submit').on('click', () => {
        if (isVerifying) return;
        isVerifying = true;
        $('#game_hash_input').parent().addClass('is-loading');
        $('#game_verify_submit').addClass('is-loading');
        $('#game_hash_input, #game_amount_input, #game_verify_submit').attr('disabled', 'disabled');
        $('#game_verify_table').html('');
        let prevHash = null;
        for (let i = 0; i < $('#game_amount_input').val(); i++) {
          let hash = String(prevHash ? CryptoJS.SHA256(String(prevHash)) : $('#game_hash_input').val());
          let bust = gameResult(hash);
          setTimeout(function() {
            addTableRow(hash, bust, i)
          }, i * 1);
          prevHash = hash;
        }
      });
      $('#game_amount_input').on('keyup', () => {
        if ($('#game_amount_input').val() >= 10000) {
          if ($('#game_verify_warning').length) return;
          $('#game_verify_submit').parent().append(
            $('<span/>').attr({
              'id': 'game_verify_warning',
              'class': 'tag is-warning'
            }).text("Verifying a huge amount of games may consume more ressources from your CPU")
          );
        } else {
          if ($('#game_verify_warning').length) {
            $('#game_verify_warning').remove();
          }
        }
      });
      const addTableRow = (hash, bust, index) => {
        $('<tr/>').attr({
          'class': index === 0 ? 'is-first' : null
        }).append(
          $('<td/>').text(hash)
        ).append(
          $('<td/>').text(bust).attr({
            'class': bust === 1.98 ? 'is-at-median' : bust > 1.98 ? 'is-over-median' : 'is-under-median'
          })
        ).appendToWithIndex($('#game_verify_table'), index);
        if (index >= $('#game_amount_input').val() - 1) {
          $('#game_hash_input').parent().removeClass('is-loading');
          $('#game_verify_submit').removeClass('is-loading');
          $('#game_hash_input, #game_amount_input, #game_verify_submit').removeAttr("disabled");
          isVerifying = false;
        }
      };
      const gameResult = (seed) => {
        const nBits = 52; // number of most significant bits to use
        // 1. r = 52 most significant bits
        seed = seed.slice(0, nBits / 4);
        const r = parseInt(seed, 16);
        // 2. X = r / 2^52
        let X = r / Math.pow(2, nBits); // uniformly distributed in [0; 1)
        // 3. X = 99 / (1-X)
        X = 99 / (1 - X);
        // 4. return max(trunc(X), 100)
        const result = Math.floor(X);
        return Math.max(1, result / 100);
      };
      $.fn.appendToWithIndex = function(to, index) {
        if (!to instanceof jQuery) {
          to = $(to);
        };
        if (index === 0) {
          $(this).prependTo(to)
        } else {
          $(this).insertAfter(to.children().eq(index - 1));
        }
      };
      var hash_url = window.location.search
      if ((/\?hash=/).test(hash_url)) {
        var hash = hash_url.replace(/\?hash=/, '')
        $('#game_hash_input').val(hash)
        $('#game_verify_submit').click()
      }
    </script>
    <script>
      // tell the embed parent frame the height of the content
      if (window.parent && window.parent.parent) {
        window.parent.parent.postMessage(["resultsFrame", {
          height: document.body.getBoundingClientRect().height,
          slug: "nwu2ffkv"
        }], "*")
      }
    </script>
</body>

</html>
