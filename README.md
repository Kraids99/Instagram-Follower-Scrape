# Instagram-Follower-Scrape
Script untuk mengecek siapa yang engga follback kamu dan siapa yang tidak kamu follback

# Jika Hasilnya Beda
1. Instagram tidak selalu menampilkan semua akun misalnya
- akun baru difollow
- akun restricted
- akun sudah diunfollow tapi masih ada cache
- akun suspended
2. Scroll belum sampai akhir (coba ulangi)

# Langkah-Langkah Penggunaan
1. Buka 'Profile' Anda
2. Masuk ke 'Dev Tool' instagram (CTRL + Shift + I)
3. Buka bagian 'Console' kemudian copas kode dibawah ini

```bash
async function ReadFollowersAndFollowing() {

    // Menunggu beberapa saat
    function wait(time) {
        return new Promise(function (resolve) {
            setTimeout(function () {
                resolve();
            }, time);
        });
    }

    // Render halaman
    function reload() {
        window.scrollBy(0, 1);
        window.scrollBy(0, -1);
    }

    // Cari element followers / following
    function findSpanByText(text) {
        var spans = document.querySelectorAll('span.x1lliihq.x1plvlek.xryxfnj.x1n2onr6');

        for (var i = 0; i < spans.length; i++) {

            var actualText = spans[i].innerText.toLowerCase();

            if (actualText.includes(text.toLowerCase())) {
                return spans[i];
            }
        }
        return null;
    }

    // Cek span valid atau tidak
    async function isSpanValid(text) {
        var count = 3;
        var element;
        for (var i = 0; i < count; i++) {
            reload();
            element = findSpanByText(text);

            if (element !== null) {
                return element;
            }
            await wait(300);
        }
        return null;
    }

    // Klik close button
    function clickClose() {
        var closeBtn = document.querySelector('div[role="dialog"] svg[aria-label="Close"]');

        if (closeBtn) {
            closeBtn.closest('button').click();
            console.log('Dialog ditutup');
        } else {
            console.log('Close button tidak ditemukan');
        }
    }

    // Scroll followers / following sampai habis
    async function scroll() {
        var scrollBox = document.querySelector('div.x1rife3k');

        var height = 0;
        var count = 0;

        console.log('Mulai scrolling...');

        while (count < 5) {
            scrollBox.scrollTop = scrollBox.scrollHeight;
            await wait(1500);

            if (scrollBox.scrollHeight === height) {
                count++;
            } else {
                count = 0;
                height = scrollBox.scrollHeight;
            }
        }
        console.log('Scroll selesai');
    }

    // Baca username dari followers / following
    function readUsers() {

        var spans = document.querySelectorAll('span._ap3a._aaco._aacw._aacx._aad7._aade');
        var users = [];
        var username = '';

        for (var i = 0; i < spans.length; i++) {

            username = spans[i].innerText.trim();

            if (username !== '') {
                users.push(username);
            }
        }
        return users;
    }

    // Cari yang tidak mengikuti Anda kembali dan yang Anda tidak mengikuti kembali
    function notFollowingBack(foll1, foll2) {

        var result = [];

        for (var i = 0; i < foll2.length; i++) {

            var username = foll2[i];

            if (!foll1.includes(username)) {
                result.push(username);
            }
        }

        return result;
    }

    /* ===== MAIN ===== */

    console.log('Proses dimulai, Harap tunggu...');

    // Followers
    console.log('Membaca Followers...');
    const followersBtn = await isSpanValid('followers');
    followersBtn.click();

    await wait(2500);
    await scroll();

    const followers = readUsers();
    console.log('Jumlah Followers:', followers.length);
    console.log(followers);

    // Close dialog
    await wait(2500);
    clickClose();

    // Reload
    await wait(1200);
    reload();

    // Following
    console.log('Membaca Following...');
    const followingBtn = await isSpanValid('following');
    followingBtn.click();

    await wait(2500);
    await scroll();

    const following = readUsers();
    console.log('Jumlah Following:', following.length);
    console.log(following);

    // Close dialog
    await wait(2500);
    clickClose();

    // Reload
    await wait(1200);
    reload();

    // notFollowingBack and youAreNotFollowingBack checker
    const notFollowingYouBack = notFollowingBack(followers, following);
    const youAreNotFollowingBack = notFollowingBack(following, followers);

    console.log('Tidak mengikuti Anda kembali:', notFollowingYouBack.length);
    console.log(notFollowingYouBack);

    console.log('Anda tidak mengikuti kembali:', youAreNotFollowingBack.length);
    console.log(youAreNotFollowingBack);

    console.log('Selesai');

    return { followers, following, notFollowingYouBack, youAreNotFollowingBack };
}

ReadFollowersAndFollowing();
```
